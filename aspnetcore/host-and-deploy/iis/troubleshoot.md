---
title: IIS에서 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core 앱의 IIS(인터넷 정보 서비스) 배포에 대한 문제 진단 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313654"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="bd2af-103">IIS에서 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="bd2af-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="bd2af-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="bd2af-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd2af-105">이 문서에서는 [IIS(인터넷 정보 서비스)](/iis)를 통해 호스트할 경우 ASP.NET Core 앱 시작 문제를 진단하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="bd2af-106">이 문서의 정보는 Windows Server 및 Windows 데스크톱의 IIS에서 호스트하는 경우에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

<span data-ttu-id="bd2af-107">추가 문제 해결 항목:</span><span class="sxs-lookup"><span data-stu-id="bd2af-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="bd2af-108">또한 Azure App Service는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 및 IIS를 사용하여 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-108">Azure App Service also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps.</span></span> <span data-ttu-id="bd2af-109">Azure App Service에만 관련된 문제 해결 조언은 <xref:host-and-deploy/azure-apps/troubleshoot>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-109">For troubleshooting advice that pertains specifically to Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
* <span data-ttu-id="bd2af-110"><xref:fundamentals/error-handling>에서는 로컬 시스템에서 개발하는 동안 ASP.NET Core 앱에서 오류를 처리하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="bd2af-111">[Visual Studio를 사용하여 디버그하는 방법 알아보기](/visualstudio/debugger/getting-started-with-the-debugger)에서는 Visual Studio 디버거의 기능을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="bd2af-112">[Visual Studio Code를 사용한 디버깅](https://code.visualstudio.com/docs/editor/debugging)에서는 Visual Studio Code에 기본 제공되는 디버깅 지원에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="bd2af-113">앱 시작 오류 해결</span><span class="sxs-lookup"><span data-stu-id="bd2af-113">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="bd2af-114">ASP.NET Core 모듈 디버그 로그 사용</span><span class="sxs-lookup"><span data-stu-id="bd2af-114">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="bd2af-115">앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-115">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="bd2af-116">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-116">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="bd2af-117">응용 프로그램 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="bd2af-117">Application Event Log</span></span>

<span data-ttu-id="bd2af-118">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-118">Access the Application Event Log:</span></span>

1. <span data-ttu-id="bd2af-119">[시작] 메뉴를 열고 **이벤트 뷰어**를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-119">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="bd2af-120">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-120">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="bd2af-121">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-121">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="bd2af-122">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-122">Search for errors associated with the failing app.</span></span> <span data-ttu-id="bd2af-123">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.   </span><span class="sxs-lookup"><span data-stu-id="bd2af-123">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="bd2af-124">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="bd2af-124">Run the app at a command prompt</span></span>

<span data-ttu-id="bd2af-125">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-125">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="bd2af-126">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-126">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="bd2af-127">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="bd2af-127">Framework-dependent deployment</span></span>

<span data-ttu-id="bd2af-128">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="bd2af-128">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="bd2af-129">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-129">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="bd2af-130">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-130">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="bd2af-131">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-131">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="bd2af-132">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-132">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="bd2af-133">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-133">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="bd2af-134">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-134">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="bd2af-135">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="bd2af-135">Self-contained deployment</span></span>

<span data-ttu-id="bd2af-136">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="bd2af-136">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="bd2af-137">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-137">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="bd2af-138">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-138">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="bd2af-139">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-139">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="bd2af-140">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-140">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="bd2af-141">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-141">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="bd2af-142">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-142">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="bd2af-143">ASP.NET Core 모듈 stdout 로그</span><span class="sxs-lookup"><span data-stu-id="bd2af-143">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="bd2af-144">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="bd2af-144">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="bd2af-145">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-145">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="bd2af-146">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-146">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="bd2af-147">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-147">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="bd2af-148">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-148">Edit the *web.config* file.</span></span> <span data-ttu-id="bd2af-149">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="bd2af-149">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="bd2af-150">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-150">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="bd2af-151">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-151">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="bd2af-152">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-152">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="bd2af-153">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-153">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="bd2af-154">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-154">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="bd2af-155">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-155">Make a request to the app.</span></span>
1. <span data-ttu-id="bd2af-156">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-156">Navigate to the *logs* folder.</span></span> <span data-ttu-id="bd2af-157">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-157">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="bd2af-158">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-158">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bd2af-159">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-159">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="bd2af-160">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-160">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="bd2af-161">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-161">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="bd2af-162">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-162">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="bd2af-163">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-163">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="bd2af-164">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-164">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="bd2af-165">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-165">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bd2af-166">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-166">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="bd2af-167">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="bd2af-167">Enable the Developer Exception Page</span></span>

<span data-ttu-id="bd2af-168">`ASPNETCORE_ENVIRONMENT` [환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-168">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="bd2af-169">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-169">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="bd2af-170">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-170">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="bd2af-171">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-171">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="bd2af-172">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-172">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="bd2af-173">일반 시작 오류</span><span class="sxs-lookup"><span data-stu-id="bd2af-173">Common startup errors</span></span>

<span data-ttu-id="bd2af-174"><xref:host-and-deploy/azure-iis-errors-reference>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-174">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="bd2af-175">앱 시작을 차단하는 대부분의 일반적인 문제는 참조 항목에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-175">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="bd2af-176">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="bd2af-176">Obtain data from an app</span></span>

<span data-ttu-id="bd2af-177">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-177">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="bd2af-178">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-178">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="bd2af-179">덤프 만들기</span><span class="sxs-lookup"><span data-stu-id="bd2af-179">Create a dump</span></span>

<span data-ttu-id="bd2af-180">*덤프*는 시스템 메모리의 스냅숏이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-180">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="bd2af-181">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="bd2af-181">App crashes or encounters an exception</span></span>

<span data-ttu-id="bd2af-182">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-182">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="bd2af-183">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-183">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="bd2af-184">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-184">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="bd2af-185">[EnableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="bd2af-185">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="bd2af-186">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="bd2af-186">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="bd2af-187">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="bd2af-187">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="bd2af-188">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-188">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="bd2af-189">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="bd2af-189">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="bd2af-190">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="bd2af-190">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="bd2af-191">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="bd2af-191">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="bd2af-192">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-192">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="bd2af-193">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-193">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="bd2af-194">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-194">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="bd2af-195">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="bd2af-195">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="bd2af-196">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-196">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="bd2af-197">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="bd2af-197">Analyze the dump</span></span>

<span data-ttu-id="bd2af-198">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-198">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="bd2af-199">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-199">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="bd2af-200">원격 디버깅</span><span class="sxs-lookup"><span data-stu-id="bd2af-200">Remote debugging</span></span>

<span data-ttu-id="bd2af-201">Visual Studio 설명서에서 [Visual Studio 2017의 원격 IIS 컴퓨터에서 ASP.NET Core 원격 디버그](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-201">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="bd2af-202">응용 프로그램 정보</span><span class="sxs-lookup"><span data-stu-id="bd2af-202">Application Insights</span></span>

<span data-ttu-id="bd2af-203">[Application Insights](/azure/application-insights/)에서는 오류 로깅 및 보고 기능을 포함하여 IIS를 통해 호스트된 앱의 원격 분석을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-203">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="bd2af-204">Application Insights는 앱의 로깅 기능을 사용할 수 있게 될 때 애플리케이션이 시작된 후 발생하는 오류만 보고할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-204">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="bd2af-205">자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd2af-205">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="bd2af-206">추가적인 조언</span><span class="sxs-lookup"><span data-stu-id="bd2af-206">Additional advice</span></span>

<span data-ttu-id="bd2af-207">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드한 후에 즉시 작동 중인 앱에서 오류가 발생하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-207">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="bd2af-208">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-208">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="bd2af-209">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-209">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="bd2af-210">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-210">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="bd2af-211">*%UserProfile%\\.nuget\\packages* 및 *%LocalAppData%\\Nuget\\v3-cache*에 있는 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-211">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="bd2af-212">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-212">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="bd2af-213">서버의 이전 배포가 앱을 다시 배포하기 전에 완전히 삭제되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-213">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="bd2af-214">명령 프롬프트에서 `dotnet nuget locals all --clear`를 실행하면 간편하게 패키지 캐시를 지울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-214">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="bd2af-215">[nuget.exe](https://www.nuget.org/downloads) 도구를 사용하고 명령 `nuget locals all -clear`를 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-215">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="bd2af-216">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bd2af-216">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd2af-217">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bd2af-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
