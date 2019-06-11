---
title: Azure App Service에서 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core Azure App Service 배포에 대한 문제 진단 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 7a0bb7df27ebbea0eac79771452295846fad563a
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470448"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="d7159-103">Azure App Service에서 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="d7159-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="d7159-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="d7159-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="d7159-105">이 문서에서는 Azure App Service의 진단 도구를 사용하여 ASP.NET Core 앱 시작 문제를 진단하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="d7159-106">추가 문제 해결 권장 사항은 Azure 설명서의 [Azure App Service 진단 개요](/azure/app-service/app-service-diagnostics) 및 [방법: Azure App Service에서 앱 모니터링](/azure/app-service/web-sites-monitor)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="d7159-107">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="d7159-107">App startup errors</span></span>

<span data-ttu-id="d7159-108">**502.5 프로세스 실패** 작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-108">**502.5 Process Failure** The worker process fails.</span></span> <span data-ttu-id="d7159-109">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-109">The app doesn't start.</span></span>

<span data-ttu-id="d7159-110">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-110">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="d7159-111">애플리케이션 이벤트 로그를 검토하면 이 유형의 문제를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-111">Examining the Application Event Log often helps troubleshoot this type of problem.</span></span> <span data-ttu-id="d7159-112">[애플리케이션 이벤트 로그](#application-event-log) 섹션에서는 로그 액세스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-112">Accessing the log is explained in the [Application Event Log](#application-event-log) section.</span></span>

<span data-ttu-id="d7159-113">잘못 구성된 앱으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다. </span><span class="sxs-lookup"><span data-stu-id="d7159-113">The *502.5 Process Failure* error page is returned when a misconfigured app causes the worker process to fail:</span></span>

![502.5 프로세스 실패 페이지를 보여주는 브라우저 창](troubleshoot/_static/process-failure-page.png)

<span data-ttu-id="d7159-115">**500 내부 서버 오류**</span><span class="sxs-lookup"><span data-stu-id="d7159-115">**500 Internal Server Error**</span></span>

<span data-ttu-id="d7159-116">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-116">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="d7159-117">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-117">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="d7159-118">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="d7159-118">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="d7159-119">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-119">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="d7159-120">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-120">From the server's perspective, that's correct.</span></span> <span data-ttu-id="d7159-121">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-121">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="d7159-122">[Kudu 콘솔에서 앱을 실행](#run-the-app-in-the-kudu-console)하거나 [ASP.NET Core 모듈 stdout 로그를 사용](#aspnet-core-module-stdout-log)하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-122">[Run the app in the Kudu console](#run-the-app-in-the-kudu-console) or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="d7159-123">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="d7159-123">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="d7159-124">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-124">The worker process fails.</span></span> <span data-ttu-id="d7159-125">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-125">The app doesn't start.</span></span>

<span data-ttu-id="d7159-126">ASP.NET Core 모듈이 .NET Core CLR in-process를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-126">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="d7159-127">프로세스 시작 실패의 원인은 일반적으로 [애플리케이션 이벤트 로그](#application-event-log) 및 [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log)의 항목에서 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-127">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="d7159-128">500.31 ANCM 네이티브 종속성을 찾지 못함</span><span class="sxs-lookup"><span data-stu-id="d7159-128">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="d7159-129">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-129">The worker process fails.</span></span> <span data-ttu-id="d7159-130">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-130">The app doesn't start.</span></span>

<span data-ttu-id="d7159-131">ASP.NET Core 모듈이 진행 중인 .NET Core 런타임을 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-131">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="d7159-132">이 시작 오류의 가장 일반적인 원인은 `Microsoft.NETCore.App` 또는 `Microsoft.AspNetCore.App` 런타임이 설치되어 있지 않은 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-132">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="d7159-133">앱이 대상 ASP.NET Core 3.0에 배포되고 해당 버전이 머신에 없는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-133">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="d7159-134">예제 오류 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-134">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="d7159-135">오류 메시지는 설치된 모든 .NET Core 버전과 앱에서 요청한 버전을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-135">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="d7159-136">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-136">To fix this error, either:</span></span>

* <span data-ttu-id="d7159-137">머신에 적절한 버전의 .NET Core를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-137">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="d7159-138">머신에 있는 .NET Core 버전을 대상으로 앱을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-138">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="d7159-139">[자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-139">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="d7159-140">개발 중에 실행될 때(`ASPNETCORE_ENVIRONMENT` 환경 변수가 `Development`로 설정됨) 특정 오류가 HTTP 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-140">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="d7159-141">프로세스 시작 실패의 원인은 [애플리케이션 이벤트 로그](#application-event-log)에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-141">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="d7159-142">500.32 ANCM dll을 로드하지 못함</span><span class="sxs-lookup"><span data-stu-id="d7159-142">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="d7159-143">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-143">The worker process fails.</span></span> <span data-ttu-id="d7159-144">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-144">The app doesn't start.</span></span>

<span data-ttu-id="d7159-145">이 오류의 가장 일반적인 원인은 앱이 호환되지 않는 프로세서 아키텍처에 대해 게시되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-145">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="d7159-146">작업자 프로세스가 32비트 앱으로 실행 중이고 해당 앱이 대상 64 비트로 게시된 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-146">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="d7159-147">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-147">To fix this error, either:</span></span>

* <span data-ttu-id="d7159-148">작업자 프로세스와 동일한 프로세서 아키텍처에 대해 앱을 다시 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-148">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="d7159-149">앱을 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-executables-fde)로 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-149">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="d7159-150">500.33 ANCM 요청 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="d7159-150">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="d7159-151">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-151">The worker process fails.</span></span> <span data-ttu-id="d7159-152">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-152">The app doesn't start.</span></span>

<span data-ttu-id="d7159-153">앱이 `Microsoft.AspNetCore.App` 프레임워크를 참조하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-153">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="d7159-154">`Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는 앱만 ASP.NET Core 모듈에서 호스팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-154">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the ASP.NET Core Module.</span></span>

<span data-ttu-id="d7159-155">이 오류를 해결하려면 앱이 `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-155">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="d7159-156">`.runtimeconfig.json`을 확인하여 앱이 대상으로 하는 프레임워크를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-156">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="d7159-157">500.34 ANCM 혼합된 호스팅 모델이 지원되지 않음</span><span class="sxs-lookup"><span data-stu-id="d7159-157">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="d7159-158">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-158">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="d7159-159">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-159">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="d7159-160">500.35 ANCM 동일한 프로세스의 여러 In-Process 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="d7159-160">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="d7159-161">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-161">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="d7159-162">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-162">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="d7159-163">500.36 ANCM Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="d7159-163">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="d7159-164">Out-of-process 요청 처리기, *aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 파일 옆에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-164">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="d7159-165">이는 ASP.NET Core 모듈의 손상된 설치를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-165">This indicates a corrupted installation of the ASP.NET Core Module.</span></span>

<span data-ttu-id="d7159-166">이 오류를 해결하려면 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)(IIS의 경우) 또는 Visual Studio(IIS Express의 경우)의 설치를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-166">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="d7159-167">500.37 ANCM 시작 시간 제한 내에 시작하지 못함</span><span class="sxs-lookup"><span data-stu-id="d7159-167">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="d7159-168">ANCM은 제공된 시작 시간 제한 내에 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-168">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="d7159-169">기본적으로 제한 시간은 120초입니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-169">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="d7159-170">이 오류는 동일한 머신에서 많은 수의 앱을 시작할 때 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-170">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="d7159-171">시작하는 동안 서버에서 CPU/메모리 사용량이 급증하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-171">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="d7159-172">여러 앱의 시작 프로세스를 분산해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-172">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="d7159-173">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="d7159-173">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="d7159-174">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-174">The worker process fails.</span></span> <span data-ttu-id="d7159-175">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-175">The app doesn't start.</span></span>

<span data-ttu-id="d7159-176">ASP.NET Core 모듈이 진행 중인 .NET Core 런타임을 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-176">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="d7159-177">프로세스 시작 실패의 원인은 일반적으로 [애플리케이션 이벤트 로그](#application-event-log) 및 [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log)의 항목에서 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-177">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="d7159-178">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="d7159-178">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="d7159-179">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-179">The worker process fails.</span></span> <span data-ttu-id="d7159-180">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-180">The app doesn't start.</span></span>

<span data-ttu-id="d7159-181">프로세스 시작 실패의 원인은 [애플리케이션 이벤트 로그](#application-event-log)에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-181">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

::: moniker-end

<span data-ttu-id="d7159-182">**연결 다시 설정**</span><span class="sxs-lookup"><span data-stu-id="d7159-182">**Connection reset**</span></span>

<span data-ttu-id="d7159-183">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-183">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="d7159-184">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-184">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="d7159-185">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다. </span><span class="sxs-lookup"><span data-stu-id="d7159-185">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="d7159-186">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-186">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="d7159-187">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="d7159-187">Default startup limits</span></span>

<span data-ttu-id="d7159-188">ASP.NET Core 모듈은 기본 *startupTimeLimit*이 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-188">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="d7159-189">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-189">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="d7159-190">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-190">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="d7159-191">앱 시작 오류 해결</span><span class="sxs-lookup"><span data-stu-id="d7159-191">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="d7159-192">응용 프로그램 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="d7159-192">Application Event Log</span></span>

<span data-ttu-id="d7159-193">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-193">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="d7159-194">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-194">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="d7159-195">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-195">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="d7159-196">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-196">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="d7159-197">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-197">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="d7159-198">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-198">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="d7159-199">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-199">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="d7159-200">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-200">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="d7159-201">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-201">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="d7159-202">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-202">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="d7159-203">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-203">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="d7159-204">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-204">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="d7159-205">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-205">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="d7159-206">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-206">Examine the log.</span></span> <span data-ttu-id="d7159-207">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-207">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="d7159-208">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="d7159-208">Run the app in the Kudu console</span></span>

<span data-ttu-id="d7159-209">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-209">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="d7159-210">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-210">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="d7159-211">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-211">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="d7159-212">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-212">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="d7159-213">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-213">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="d7159-214">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-214">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="d7159-215">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="d7159-215">Test a 32-bit (x86) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="d7159-216">현재 릴리스</span><span class="sxs-lookup"><span data-stu-id="d7159-216">Current release</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="d7159-217">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-217">Run the app:</span></span>
   * <span data-ttu-id="d7159-218">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="d7159-218">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="d7159-219">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="d7159-219">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="d7159-220">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-220">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="d7159-221">미리 보기 릴리스에서 실행되는 프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="d7159-221">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="d7159-222">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다. </span><span class="sxs-lookup"><span data-stu-id="d7159-222">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="d7159-223">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="d7159-223">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="d7159-224">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="d7159-224">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="d7159-225">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-225">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="d7159-226">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="d7159-226">Test a 64-bit (x64) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="d7159-227">현재 릴리스</span><span class="sxs-lookup"><span data-stu-id="d7159-227">Current release</span></span>

* <span data-ttu-id="d7159-228">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="d7159-228">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="d7159-229">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="d7159-229">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="d7159-230">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="d7159-230">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="d7159-231">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="d7159-231">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="d7159-232">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-232">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="d7159-233">미리 보기 릴리스에서 실행되는 프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="d7159-233">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="d7159-234">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다. </span><span class="sxs-lookup"><span data-stu-id="d7159-234">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="d7159-235">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="d7159-235">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="d7159-236">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="d7159-236">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="d7159-237">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-237">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="d7159-238">ASP.NET Core 모듈 stdout 로그</span><span class="sxs-lookup"><span data-stu-id="d7159-238">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="d7159-239">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-239">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="d7159-240">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="d7159-240">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="d7159-241">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-241">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="d7159-242">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-242">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="d7159-243">**추천 솔루션** > **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-243">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="d7159-244">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-244">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="d7159-245">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-245">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="d7159-246">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-246">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="d7159-247">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-247">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="d7159-248">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-248">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="d7159-249">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-249">Make a request to the app.</span></span>
1. <span data-ttu-id="d7159-250">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-250">Return to the Azure portal.</span></span> <span data-ttu-id="d7159-251">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-251">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="d7159-252">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-252">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="d7159-253">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-253">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="d7159-254">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-254">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="d7159-255">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-255">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="d7159-256">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-256">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="d7159-257">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-257">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="d7159-258">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-258">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="d7159-259">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-259">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="d7159-260">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-260">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="d7159-261">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-261">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="d7159-262">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-262">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="d7159-263">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-263">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="d7159-264">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-264">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="d7159-265">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-265">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="d7159-266">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-266">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="d7159-267">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a><span data-ttu-id="d7159-268">ASP.NET Core 모듈 디버그 로그</span><span class="sxs-lookup"><span data-stu-id="d7159-268">ASP.NET Core Module debug log</span></span>

<span data-ttu-id="d7159-269">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-269">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="d7159-270">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="d7159-270">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="d7159-271">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-271">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="d7159-272">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-272">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="d7159-273">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-273">Redeploy the app.</span></span>
   * <span data-ttu-id="d7159-274">Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-274">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="d7159-275">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-275">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="d7159-276">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-276">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="d7159-277">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-277">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="d7159-278">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-278">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="d7159-279">폴더를 **site** >  **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-279">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="d7159-280">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-280">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="d7159-281">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-281">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="d7159-282">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-282">Select the **Save** button.</span></span>
1. <span data-ttu-id="d7159-283">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-283">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="d7159-284">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-284">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="d7159-285">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-285">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="d7159-286">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-286">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="d7159-287">폴더를 **site** >  **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-287">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="d7159-288">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-288">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="d7159-289">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-289">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="d7159-290">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-290">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="d7159-291">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-291">Disable debug logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="d7159-292">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-292">To disable the enhanced debug log, perform either of the following:</span></span>
   * <span data-ttu-id="d7159-293">*web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-293">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
   * <span data-ttu-id="d7159-294">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-294">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="d7159-295">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-295">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="d7159-296">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-296">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="d7159-297">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-297">There's no limit on log file size.</span></span> <span data-ttu-id="d7159-298">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-298">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="d7159-299">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-299">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="d7159-300">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-300">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

## <a name="common-startup-errors"></a><span data-ttu-id="d7159-301">일반 시작 오류</span><span class="sxs-lookup"><span data-stu-id="d7159-301">Common startup errors</span></span>

<span data-ttu-id="d7159-302"><xref:host-and-deploy/azure-iis-errors-reference>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-302">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="d7159-303">앱 시작을 차단하는 대부분의 일반적인 문제는 참조 항목에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-303">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="d7159-304">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="d7159-304">Slow or hanging app</span></span>

<span data-ttu-id="d7159-305">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-305">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="d7159-306">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="d7159-306">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="d7159-307">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="d7159-307">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a><span data-ttu-id="d7159-308">원격 디버깅</span><span class="sxs-lookup"><span data-stu-id="d7159-308">Remote debugging</span></span>

<span data-ttu-id="d7159-309">다음 항목을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="d7159-309">See the following topics:</span></span>

* <span data-ttu-id="d7159-310">[Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="d7159-310">[Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure documentation)</span></span>
* <span data-ttu-id="d7159-311">[Visual Studio 2017의 Azure에서 IIS 기반 ASP.NET Core 원격 디버그](/visualstudio/debugger/remote-debugging-azure)(Visual Studio 설명서)</span><span class="sxs-lookup"><span data-stu-id="d7159-311">[Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (Visual Studio documentation)</span></span>

## <a name="application-insights"></a><span data-ttu-id="d7159-312">응용 프로그램 정보</span><span class="sxs-lookup"><span data-stu-id="d7159-312">Application Insights</span></span>

<span data-ttu-id="d7159-313">[Application Insights](https://azure.microsoft.com/services/application-insights/)에서는 오류 로깅 및 보고 기능을 포함하여 Azure App Service에 호스트된 앱의 원격 분석을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-313">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="d7159-314">Application Insights는 앱의 로깅 기능을 사용할 수 있게 될 때 애플리케이션이 시작된 후 발생하는 오류만 보고할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-314">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="d7159-315">자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-315">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="d7159-316">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="d7159-316">Monitoring blades</span></span>

<span data-ttu-id="d7159-317">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-317">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="d7159-318">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-318">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="d7159-319">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-319">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="d7159-320">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-320">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="d7159-321">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-321">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="d7159-322">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-322">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="d7159-323">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-323">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="d7159-324">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-324">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="d7159-325">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-325">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="d7159-326">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-326">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="d7159-327">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-327">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="d7159-328">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-328">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="d7159-329">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-329">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="d7159-330">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-330">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="d7159-331">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-331">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="d7159-332">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-332">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="d7159-333">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-333">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="d7159-334">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-334">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="d7159-335">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-335">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="d7159-336">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-336">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="d7159-337">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-337">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="d7159-338">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-338">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="d7159-339">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-339">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="d7159-340">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-340">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="d7159-341">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-341">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="d7159-342">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-342">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="d7159-343">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-343">Make a request to the app.</span></span>
1. <span data-ttu-id="d7159-344">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-344">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="d7159-345">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-345">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="d7159-346">[ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log) 섹션의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-346">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="d7159-347">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="d7159-347">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="d7159-348">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-348">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="d7159-349">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-349">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="d7159-350">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-350">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="d7159-351">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-351">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="d7159-352">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-352">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="d7159-353">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-353">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="d7159-354">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7159-354">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="d7159-355">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7159-355">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d7159-356">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d7159-356">Additional resources</span></span>

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="d7159-357">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="d7159-357">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="d7159-358">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="d7159-358">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="d7159-359">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="d7159-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="d7159-360">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="d7159-360">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="d7159-361">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="d7159-361">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="d7159-362">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="d7159-362">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
