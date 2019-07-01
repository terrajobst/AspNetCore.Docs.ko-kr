---
title: Azure App Service에서 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core Azure App Service 배포에 대한 문제 진단 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: d78499c1a82a011239f6b62b546f304a5d5017e2
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313752"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="c97b4-103">Azure App Service에서 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="c97b4-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="c97b4-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="c97b4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="c97b4-105">이 문서에서는 Azure App Service의 진단 도구를 사용하여 ASP.NET Core 앱 시작 문제를 진단하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="c97b4-106">추가 문제 해결 권장 사항은 Azure 설명서의 [Azure App Service 진단 개요](/azure/app-service/app-service-diagnostics) 및 [방법: Azure App Service에서 앱 모니터링](/azure/app-service/web-sites-monitor)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

<span data-ttu-id="c97b4-107">추가 문제 해결 항목:</span><span class="sxs-lookup"><span data-stu-id="c97b4-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="c97b4-108">IIS는 앱을 호스트하기 위해 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)도 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-108">IIS also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host apps.</span></span> <span data-ttu-id="c97b4-109">IIS에만 관련된 문제 해결 조언은 <xref:host-and-deploy/iis/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-109">For troubleshooting advice that pertains specifically to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>
* <span data-ttu-id="c97b4-110"><xref:fundamentals/error-handling>에서는 로컬 시스템에서 개발하는 동안 ASP.NET Core 앱에서 오류를 처리하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="c97b4-111">[Visual Studio를 사용하여 디버그하는 방법 알아보기](/visualstudio/debugger/getting-started-with-the-debugger)에서는 Visual Studio 디버거의 기능을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="c97b4-112">[Visual Studio Code를 사용한 디버깅](https://code.visualstudio.com/docs/editor/debugging)에서는 Visual Studio Code에 기본 제공되는 디버깅 지원에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="c97b4-113">앱 시작 오류 해결</span><span class="sxs-lookup"><span data-stu-id="c97b4-113">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="c97b4-114">응용 프로그램 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="c97b4-114">Application Event Log</span></span>

<span data-ttu-id="c97b4-115">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-115">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="c97b4-116">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-116">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="c97b4-117">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-117">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="c97b4-118">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-118">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="c97b4-119">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-119">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="c97b4-120">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-120">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="c97b4-121">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-121">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="c97b4-122">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-122">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c97b4-123">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-123">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c97b4-124">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-124">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c97b4-125">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-125">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c97b4-126">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-126">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c97b4-127">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-127">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="c97b4-128">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-128">Examine the log.</span></span> <span data-ttu-id="c97b4-129">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-129">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="c97b4-130">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="c97b4-130">Run the app in the Kudu console</span></span>

<span data-ttu-id="c97b4-131">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-131">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c97b4-132">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-132">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="c97b4-133">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-133">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c97b4-134">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-134">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c97b4-135">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-135">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c97b4-136">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-136">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="c97b4-137">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="c97b4-137">Test a 32-bit (x86) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="c97b4-138">현재 릴리스</span><span class="sxs-lookup"><span data-stu-id="c97b4-138">Current release</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="c97b4-139">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-139">Run the app:</span></span>
   * <span data-ttu-id="c97b4-140">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="c97b4-140">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="c97b4-141">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="c97b4-141">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="c97b4-142">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-142">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="c97b4-143">미리 보기 릴리스에서 실행되는 프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="c97b4-143">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="c97b4-144">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다. </span><span class="sxs-lookup"><span data-stu-id="c97b4-144">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="c97b4-145">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="c97b4-145">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c97b4-146">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c97b4-146">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c97b4-147">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-147">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="c97b4-148">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="c97b4-148">Test a 64-bit (x64) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="c97b4-149">현재 릴리스</span><span class="sxs-lookup"><span data-stu-id="c97b4-149">Current release</span></span>

* <span data-ttu-id="c97b4-150">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="c97b4-150">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="c97b4-151">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c97b4-151">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="c97b4-152">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="c97b4-152">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="c97b4-153">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="c97b4-153">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="c97b4-154">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-154">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="c97b4-155">미리 보기 릴리스에서 실행되는 프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="c97b4-155">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="c97b4-156">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다. </span><span class="sxs-lookup"><span data-stu-id="c97b4-156">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="c97b4-157">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="c97b4-157">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c97b4-158">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c97b4-158">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c97b4-159">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-159">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="c97b4-160">ASP.NET Core 모듈 stdout 로그</span><span class="sxs-lookup"><span data-stu-id="c97b4-160">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="c97b4-161">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-161">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="c97b4-162">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="c97b4-162">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c97b4-163">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-163">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c97b4-164">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-164">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="c97b4-165">**추천 솔루션** > **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-165">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="c97b4-166">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-166">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c97b4-167">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-167">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c97b4-168">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-168">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c97b4-169">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-169">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c97b4-170">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-170">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c97b4-171">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-171">Make a request to the app.</span></span>
1. <span data-ttu-id="c97b4-172">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-172">Return to the Azure portal.</span></span> <span data-ttu-id="c97b4-173">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-173">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c97b4-174">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-174">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c97b4-175">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-175">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c97b4-176">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-176">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c97b4-177">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-177">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c97b4-178">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-178">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="c97b4-179">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-179">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="c97b4-180">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-180">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c97b4-181">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-181">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="c97b4-182">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-182">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="c97b4-183">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-183">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c97b4-184">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-184">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="c97b4-185">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-185">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c97b4-186">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-186">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="c97b4-187">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-187">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c97b4-188">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-188">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c97b4-189">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-189">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a><span data-ttu-id="c97b4-190">ASP.NET Core 모듈 디버그 로그</span><span class="sxs-lookup"><span data-stu-id="c97b4-190">ASP.NET Core Module debug log</span></span>

<span data-ttu-id="c97b4-191">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-191">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="c97b4-192">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="c97b4-192">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c97b4-193">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-193">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="c97b4-194">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-194">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="c97b4-195">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-195">Redeploy the app.</span></span>
   * <span data-ttu-id="c97b4-196">Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-196">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="c97b4-197">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-197">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c97b4-198">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-198">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c97b4-199">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-199">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="c97b4-200">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-200">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="c97b4-201">폴더를 **site** >  **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-201">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c97b4-202">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-202">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="c97b4-203">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-203">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="c97b4-204">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-204">Select the **Save** button.</span></span>
1. <span data-ttu-id="c97b4-205">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-205">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c97b4-206">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-206">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c97b4-207">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-207">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c97b4-208">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-208">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c97b4-209">폴더를 **site** >  **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-209">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c97b4-210">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-210">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="c97b4-211">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-211">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="c97b4-212">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-212">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="c97b4-213">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-213">Disable debug logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c97b4-214">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-214">To disable the enhanced debug log, perform either of the following:</span></span>
   * <span data-ttu-id="c97b4-215">*web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-215">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
   * <span data-ttu-id="c97b4-216">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-216">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="c97b4-217">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-217">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="c97b4-218">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-218">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="c97b4-219">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-219">There's no limit on log file size.</span></span> <span data-ttu-id="c97b4-220">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-220">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c97b4-221">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-221">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c97b4-222">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-222">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

## <a name="common-startup-errors"></a><span data-ttu-id="c97b4-223">일반 시작 오류</span><span class="sxs-lookup"><span data-stu-id="c97b4-223">Common startup errors</span></span>

<span data-ttu-id="c97b4-224"><xref:host-and-deploy/azure-iis-errors-reference>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-224">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="c97b4-225">앱 시작을 차단하는 대부분의 일반적인 문제는 참조 항목에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-225">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="c97b4-226">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="c97b4-226">Slow or hanging app</span></span>

<span data-ttu-id="c97b4-227">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-227">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="c97b4-228">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="c97b4-228">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c97b4-229">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="c97b4-229">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a><span data-ttu-id="c97b4-230">원격 디버깅</span><span class="sxs-lookup"><span data-stu-id="c97b4-230">Remote debugging</span></span>

<span data-ttu-id="c97b4-231">다음 항목을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c97b4-231">See the following topics:</span></span>

* <span data-ttu-id="c97b4-232">[Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="c97b4-232">[Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure documentation)</span></span>
* <span data-ttu-id="c97b4-233">[Visual Studio 2017의 Azure에서 IIS 기반 ASP.NET Core 원격 디버그](/visualstudio/debugger/remote-debugging-azure)(Visual Studio 설명서)</span><span class="sxs-lookup"><span data-stu-id="c97b4-233">[Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (Visual Studio documentation)</span></span>

## <a name="application-insights"></a><span data-ttu-id="c97b4-234">응용 프로그램 정보</span><span class="sxs-lookup"><span data-stu-id="c97b4-234">Application Insights</span></span>

<span data-ttu-id="c97b4-235">[Application Insights](https://azure.microsoft.com/services/application-insights/)에서는 오류 로깅 및 보고 기능을 포함하여 Azure App Service에 호스트된 앱의 원격 분석을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-235">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="c97b4-236">Application Insights는 앱의 로깅 기능을 사용할 수 있게 될 때 애플리케이션이 시작된 후 발생하는 오류만 보고할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-236">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="c97b4-237">자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-237">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="c97b4-238">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="c97b4-238">Monitoring blades</span></span>

<span data-ttu-id="c97b4-239">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-239">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="c97b4-240">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-240">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="c97b4-241">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-241">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="c97b4-242">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-242">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="c97b4-243">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-243">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="c97b4-244">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-244">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="c97b4-245">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-245">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="c97b4-246">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-246">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="c97b4-247">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-247">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c97b4-248">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-248">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="c97b4-249">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-249">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="c97b4-250">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-250">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="c97b4-251">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-251">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c97b4-252">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-252">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c97b4-253">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-253">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c97b4-254">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-254">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c97b4-255">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-255">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c97b4-256">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-256">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c97b4-257">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-257">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c97b4-258">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-258">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="c97b4-259">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-259">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="c97b4-260">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-260">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="c97b4-261">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-261">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="c97b4-262">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-262">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c97b4-263">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-263">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="c97b4-264">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-264">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="c97b4-265">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-265">Make a request to the app.</span></span>
1. <span data-ttu-id="c97b4-266">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-266">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="c97b4-267">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-267">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="c97b4-268">[ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log) 섹션의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-268">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="c97b4-269">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="c97b4-269">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="c97b4-270">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-270">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c97b4-271">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-271">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="c97b4-272">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-272">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="c97b4-273">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-273">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="c97b4-274">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-274">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c97b4-275">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-275">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c97b4-276">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c97b4-276">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c97b4-277">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c97b4-277">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c97b4-278">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c97b4-278">Additional resources</span></span>

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="c97b4-279">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="c97b4-279">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="c97b4-280">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="c97b4-280">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="c97b4-281">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="c97b4-281">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c97b4-282">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="c97b4-282">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="c97b4-283">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="c97b4-283">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="c97b4-284">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="c97b4-284">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
