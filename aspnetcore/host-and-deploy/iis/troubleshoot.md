---
title: IIS에서 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core 앱의 IIS(인터넷 정보 서비스) 배포에 대한 문제 진단 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: e4c93459f2030c7c0a55ea90e0cc8c8d30b76c51
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470452"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="29e4f-103">IIS에서 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="29e4f-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="29e4f-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="29e4f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="29e4f-105">이 문서에서는 [IIS(인터넷 정보 서비스)](/iis)를 통해 호스트할 경우 ASP.NET Core 앱 시작 문제를 진단하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="29e4f-106">이 문서의 정보는 Windows Server 및 Windows 데스크톱의 IIS에서 호스트하는 경우에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="29e4f-107">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="29e4f-108">로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패*는 이 항목의 권장 사항을 사용하여 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="29e4f-109">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="29e4f-110">로컬에서 디버그할 때 발생하는 *502.5 프로세스 실패*는 이 항목의 권장 사항을 사용하여 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="29e4f-111">추가 문제 해결 항목:</span><span class="sxs-lookup"><span data-stu-id="29e4f-111">Additional troubleshooting topics:</span></span>

<span data-ttu-id="29e4f-112"><xref:host-and-deploy/azure-apps/troubleshoot>App Service는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 IIS를 사용하여 앱을 호스트하지만 App Service 관련 지침은 전용 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-112"><xref:host-and-deploy/azure-apps/troubleshoot> Although App Service uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<span data-ttu-id="29e4f-113"><xref:fundamentals/error-handling>로컬 시스템에서 개발하는 동안 ASP.NET Core 앱에서 오류를 처리하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-113"><xref:fundamentals/error-handling> Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

<span data-ttu-id="29e4f-114">[Visual Studio를 사용하여 디버그하는 방법 알아보기](/visualstudio/debugger/getting-started-with-the-debugger) 이 항목에서는 Visual Studio 디버거의 기능을 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-114">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) This topic introduces the features of the Visual Studio debugger.</span></span>

<span data-ttu-id="29e4f-115">[Visual Studio Code를 사용한 디버깅](https://code.visualstudio.com/docs/editor/debugging) Visual Studio Code에 기본 제공되는 디버깅 지원을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-115">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="29e4f-116">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="29e4f-116">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="29e4f-117">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-117">502.5 Process Failure</span></span>

<span data-ttu-id="29e4f-118">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-118">The worker process fails.</span></span> <span data-ttu-id="29e4f-119">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-119">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-120">ASP.NET Core 모듈이 백엔드 dotnet 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-120">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="29e4f-121">프로세스 시작 실패의 원인은 일반적으로 [애플리케이션 이벤트 로그](#application-event-log) 및 [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log)의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-121">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="29e4f-122">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-122">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="29e4f-123">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-123">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="29e4f-124">공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다. </span><span class="sxs-lookup"><span data-stu-id="29e4f-124">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="29e4f-125">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-125">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="29e4f-126">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-126">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="29e4f-127">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다. </span><span class="sxs-lookup"><span data-stu-id="29e4f-127">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![502.5 프로세스 실패 페이지를 보여주는 브라우저 창](troubleshoot/_static/process-failure-page.png)

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="29e4f-129">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-129">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="29e4f-130">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-130">The worker process fails.</span></span> <span data-ttu-id="29e4f-131">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-131">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-132">ASP.NET Core 모듈이 .NET Core CLR in-process를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-132">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="29e4f-133">프로세스 시작 실패의 원인은 일반적으로 [애플리케이션 이벤트 로그](#application-event-log) 및 [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log)의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-133">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="29e4f-134">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-134">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="29e4f-135">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-135">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="29e4f-136">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-136">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="29e4f-137">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-137">The worker process fails.</span></span> <span data-ttu-id="29e4f-138">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-138">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-139">ASP.NET Core 모듈이 .NET Core CLR을 찾지 못하고 in-process 요청 처리기(*aspnetcorev2_inprocess.dll*)를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-139">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="29e4f-140">다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-140">Check that:</span></span>

* <span data-ttu-id="29e4f-141">앱은 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 패키지 또는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 중 하나를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-141">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="29e4f-142">앱이 대상으로 하는 ASP.NET Core 공유 프레임워크의 버전이 대상 머신에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-142">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="29e4f-143">500.0 Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-143">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="29e4f-144">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-144">The worker process fails.</span></span> <span data-ttu-id="29e4f-145">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-145">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-146">ASP.NET Core 모듈이 out-of-process 호스팅 요청 처리기를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-146">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="29e4f-147">*aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 옆의 하위 폴더에 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-147">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="29e4f-148">500.31 ANCM 네이티브 종속성을 찾지 못함</span><span class="sxs-lookup"><span data-stu-id="29e4f-148">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="29e4f-149">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-149">The worker process fails.</span></span> <span data-ttu-id="29e4f-150">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-150">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-151">ASP.NET Core 모듈이 진행 중인 .NET Core 런타임을 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-151">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="29e4f-152">이 시작 오류의 가장 일반적인 원인은 `Microsoft.NETCore.App` 또는 `Microsoft.AspNetCore.App` 런타임이 설치되어 있지 않은 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-152">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="29e4f-153">앱이 대상 ASP.NET Core 3.0에 배포되고 해당 버전이 머신에 없는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-153">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="29e4f-154">예제 오류 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-154">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="29e4f-155">오류 메시지는 설치된 모든 .NET Core 버전과 앱에서 요청한 버전을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-155">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="29e4f-156">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-156">To fix this error, either:</span></span>

* <span data-ttu-id="29e4f-157">머신에 적절한 버전의 .NET Core를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-157">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="29e4f-158">머신에 있는 .NET Core 버전을 대상으로 앱을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-158">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="29e4f-159">[자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-159">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="29e4f-160">개발 중에 실행될 때(`ASPNETCORE_ENVIRONMENT` 환경 변수가 `Development`로 설정됨) 특정 오류가 HTTP 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-160">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="29e4f-161">프로세스 시작 실패의 원인은 [애플리케이션 이벤트 로그](#application-event-log)에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-161">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="29e4f-162">500.32 ANCM dll을 로드하지 못함</span><span class="sxs-lookup"><span data-stu-id="29e4f-162">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="29e4f-163">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-163">The worker process fails.</span></span> <span data-ttu-id="29e4f-164">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-164">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-165">이 오류의 가장 일반적인 원인은 앱이 호환되지 않는 프로세서 아키텍처에 대해 게시되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-165">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="29e4f-166">작업자 프로세스가 32비트 앱으로 실행 중이고 해당 앱이 대상 64 비트로 게시된 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-166">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="29e4f-167">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-167">To fix this error, either:</span></span>

* <span data-ttu-id="29e4f-168">작업자 프로세스와 동일한 프로세서 아키텍처에 대해 앱을 다시 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-168">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="29e4f-169">앱을 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-executables-fde)로 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-169">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="29e4f-170">500.33 ANCM 요청 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-170">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="29e4f-171">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-171">The worker process fails.</span></span> <span data-ttu-id="29e4f-172">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-172">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-173">앱이 `Microsoft.AspNetCore.App` 프레임워크를 참조하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-173">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="29e4f-174">`Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는 앱만 ASP.NET Core 모듈에서 호스팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-174">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the ASP.NET Core Module.</span></span>

<span data-ttu-id="29e4f-175">이 오류를 해결하려면 앱이 `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-175">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="29e4f-176">`.runtimeconfig.json`을 확인하여 앱이 대상으로 하는 프레임워크를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-176">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="29e4f-177">500.34 ANCM 혼합된 호스팅 모델이 지원되지 않음</span><span class="sxs-lookup"><span data-stu-id="29e4f-177">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="29e4f-178">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-178">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="29e4f-179">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-179">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="29e4f-180">500.35 ANCM 동일한 프로세스의 여러 In-Process 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="29e4f-180">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="29e4f-181">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-181">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="29e4f-182">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-182">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="29e4f-183">500.36 ANCM Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-183">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="29e4f-184">Out-of-process 요청 처리기, *aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 파일 옆에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-184">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="29e4f-185">이는 ASP.NET Core 모듈의 손상된 설치를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-185">This indicates a corrupted installation of the ASP.NET Core Module.</span></span>

<span data-ttu-id="29e4f-186">이 오류를 해결하려면 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)(IIS의 경우) 또는 Visual Studio(IIS Express의 경우)의 설치를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-186">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="29e4f-187">500.37 ANCM 시작 시간 제한 내에 시작하지 못함</span><span class="sxs-lookup"><span data-stu-id="29e4f-187">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="29e4f-188">ANCM은 제공된 시작 시간 제한 내에 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-188">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="29e4f-189">기본적으로 제한 시간은 120초입니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-189">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="29e4f-190">이 오류는 동일한 머신에서 많은 수의 앱을 시작할 때 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-190">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="29e4f-191">시작하는 동안 서버에서 CPU/메모리 사용량이 급증하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-191">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="29e4f-192">여러 앱의 시작 프로세스를 분산해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-192">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="29e4f-193">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-193">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="29e4f-194">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-194">The worker process fails.</span></span> <span data-ttu-id="29e4f-195">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-195">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-196">ASP.NET Core 모듈이 진행 중인 .NET Core 런타임을 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-196">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="29e4f-197">프로세스 시작 실패의 원인은 일반적으로 [애플리케이션 이벤트 로그](#application-event-log) 및 [ASP.NET Core 모듈 stdout 로그](#aspnet-core-module-stdout-log)의 항목에서 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-197">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="29e4f-198">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="29e4f-198">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="29e4f-199">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-199">The worker process fails.</span></span> <span data-ttu-id="29e4f-200">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-200">The app doesn't start.</span></span>

<span data-ttu-id="29e4f-201">ASP.NET Core 모듈 구성 요소를 로드하는 중 알 수 없는 오류가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-201">An unknown error occurred loading ASP.NET Core Module components.</span></span> <span data-ttu-id="29e4f-202">다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-202">Take one of the following actions:</span></span>

* <span data-ttu-id="29e4f-203">[Microsoft 지원](https://support.microsoft.com/oas/default.aspx?prid=15832)에 문의하세요(**개발자 도구**를 선택한 다음, **ASP.NET Core** 선택).</span><span class="sxs-lookup"><span data-stu-id="29e4f-203">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="29e4f-204">Stack Overflow에 대해 질문하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-204">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="29e4f-205">[GitHub 리포지토리](https://github.com/aspnet/AspNetCore)에 문제를 제기하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-205">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="29e4f-206">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="29e4f-206">500 Internal Server Error</span></span>

<span data-ttu-id="29e4f-207">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-207">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="29e4f-208">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-208">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="29e4f-209">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="29e4f-209">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="29e4f-210">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-210">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="29e4f-211">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-211">From the server's perspective, that's correct.</span></span> <span data-ttu-id="29e4f-212">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-212">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="29e4f-213">서버의 [명령 프롬프트에서 앱을 실행](#run-the-app-at-a-command-prompt)하거나 [ASP.NET Core 모듈 stdout 로그를 사용](#aspnet-core-module-stdout-log)하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-213">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="29e4f-214">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="29e4f-214">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="29e4f-215">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-215">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="29e4f-216">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-216">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="29e4f-217">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-217">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="29e4f-218">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-218">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="29e4f-219">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-219">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="29e4f-220">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-220">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="29e4f-221">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-221">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="29e4f-222">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-222">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="29e4f-223">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="29e4f-223">Connection reset</span></span>

<span data-ttu-id="29e4f-224">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-224">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="29e4f-225">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-225">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="29e4f-226">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다. </span><span class="sxs-lookup"><span data-stu-id="29e4f-226">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="29e4f-227">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-227">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="29e4f-228">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="29e4f-228">Default startup limits</span></span>

<span data-ttu-id="29e4f-229">ASP.NET Core 모듈은 기본 *startupTimeLimit*이 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-229">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="29e4f-230">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-230">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="29e4f-231">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-231">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="29e4f-232">앱 시작 오류 해결</span><span class="sxs-lookup"><span data-stu-id="29e4f-232">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="29e4f-233">ASP.NET Core 모듈 디버그 로그 사용</span><span class="sxs-lookup"><span data-stu-id="29e4f-233">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="29e4f-234">앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-234">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="29e4f-235">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-235">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="29e4f-236">응용 프로그램 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="29e4f-236">Application Event Log</span></span>

<span data-ttu-id="29e4f-237">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-237">Access the Application Event Log:</span></span>

1. <span data-ttu-id="29e4f-238">[시작] 메뉴를 열고 **이벤트 뷰어**를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-238">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="29e4f-239">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="29e4f-240">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-240">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="29e4f-241">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-241">Search for errors associated with the failing app.</span></span> <span data-ttu-id="29e4f-242">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.   </span><span class="sxs-lookup"><span data-stu-id="29e4f-242">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="29e4f-243">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="29e4f-243">Run the app at a command prompt</span></span>

<span data-ttu-id="29e4f-244">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-244">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="29e4f-245">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="29e4f-246">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="29e4f-246">Framework-dependent deployment</span></span>

<span data-ttu-id="29e4f-247">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="29e4f-247">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="29e4f-248">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-248">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="29e4f-249">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-249">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="29e4f-250">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-250">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29e4f-251">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-251">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29e4f-252">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-252">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29e4f-253">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-253">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="29e4f-254">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="29e4f-254">Self-contained deployment</span></span>

<span data-ttu-id="29e4f-255">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="29e4f-255">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="29e4f-256">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-256">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="29e4f-257">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-257">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="29e4f-258">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-258">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="29e4f-259">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-259">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="29e4f-260">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-260">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="29e4f-261">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-261">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="29e4f-262">ASP.NET Core 모듈 stdout 로그</span><span class="sxs-lookup"><span data-stu-id="29e4f-262">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="29e4f-263">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="29e4f-263">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="29e4f-264">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-264">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="29e4f-265">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-265">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="29e4f-266">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-266">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="29e4f-267">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-267">Edit the *web.config* file.</span></span> <span data-ttu-id="29e4f-268">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="29e4f-268">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="29e4f-269">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-269">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="29e4f-270">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-270">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="29e4f-271">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-271">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="29e4f-272">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-272">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="29e4f-273">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-273">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="29e4f-274">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-274">Make a request to the app.</span></span>
1. <span data-ttu-id="29e4f-275">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-275">Navigate to the *logs* folder.</span></span> <span data-ttu-id="29e4f-276">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-276">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="29e4f-277">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-277">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="29e4f-278">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-278">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="29e4f-279">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-279">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="29e4f-280">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-280">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="29e4f-281">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-281">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="29e4f-282">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-282">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="29e4f-283">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-283">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="29e4f-284">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-284">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="29e4f-285">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-285">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="29e4f-286">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="29e4f-286">Enable the Developer Exception Page</span></span>

<span data-ttu-id="29e4f-287">`ASPNETCORE_ENVIRONMENT` [환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-287">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="29e4f-288">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-288">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="29e4f-289">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-289">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="29e4f-290">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-290">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="29e4f-291">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-291">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="29e4f-292">일반 시작 오류</span><span class="sxs-lookup"><span data-stu-id="29e4f-292">Common startup errors</span></span>

<span data-ttu-id="29e4f-293"><xref:host-and-deploy/azure-iis-errors-reference>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-293">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="29e4f-294">앱 시작을 차단하는 대부분의 일반적인 문제는 참조 항목에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-294">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="29e4f-295">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="29e4f-295">Obtain data from an app</span></span>

<span data-ttu-id="29e4f-296">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-296">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="29e4f-297">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-297">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="29e4f-298">덤프 만들기</span><span class="sxs-lookup"><span data-stu-id="29e4f-298">Create a dump</span></span>

<span data-ttu-id="29e4f-299">*덤프*는 시스템 메모리의 스냅숏이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-299">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="29e4f-300">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="29e4f-300">App crashes or encounters an exception</span></span>

<span data-ttu-id="29e4f-301">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-301">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="29e4f-302">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-302">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="29e4f-303">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-303">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="29e4f-304">[EnableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="29e4f-304">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="29e4f-305">앱에서 [in-process 호스팅 모델](xref:fundamentals/servers/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="29e4f-305">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="29e4f-306">앱에서 [out-of-process 호스팅 모델](xref:fundamentals/servers/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="29e4f-306">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="29e4f-307">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-307">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="29e4f-308">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="29e4f-308">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="29e4f-309">앱에서 [in-process 호스팅 모델](xref:fundamentals/servers/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="29e4f-309">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="29e4f-310">앱에서 [out-of-process 호스팅 모델](xref:fundamentals/servers/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="29e4f-310">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="29e4f-311">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-311">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="29e4f-312">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-312">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="29e4f-313">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-313">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="29e4f-314">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="29e4f-314">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="29e4f-315">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-315">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="29e4f-316">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="29e4f-316">Analyze the dump</span></span>

<span data-ttu-id="29e4f-317">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-317">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="29e4f-318">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-318">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="29e4f-319">원격 디버깅</span><span class="sxs-lookup"><span data-stu-id="29e4f-319">Remote debugging</span></span>

<span data-ttu-id="29e4f-320">Visual Studio 설명서에서 [Visual Studio 2017의 원격 IIS 컴퓨터에서 ASP.NET Core 원격 디버그](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-320">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="29e4f-321">응용 프로그램 정보</span><span class="sxs-lookup"><span data-stu-id="29e4f-321">Application Insights</span></span>

<span data-ttu-id="29e4f-322">[Application Insights](/azure/application-insights/)에서는 오류 로깅 및 보고 기능을 포함하여 IIS를 통해 호스트된 앱의 원격 분석을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-322">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="29e4f-323">Application Insights는 앱의 로깅 기능을 사용할 수 있게 될 때 애플리케이션이 시작된 후 발생하는 오류만 보고할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-323">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="29e4f-324">자세한 내용은 [ASP.NET Core용 Application Insights](/azure/application-insights/app-insights-asp-net-core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29e4f-324">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="29e4f-325">추가적인 조언</span><span class="sxs-lookup"><span data-stu-id="29e4f-325">Additional advice</span></span>

<span data-ttu-id="29e4f-326">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드한 후에 즉시 작동 중인 앱에서 오류가 발생하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-326">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="29e4f-327">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-327">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="29e4f-328">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-328">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="29e4f-329">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-329">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="29e4f-330">*%UserProfile%\\.nuget\\packages* 및 *%LocalAppData%\\Nuget\\v3-cache*에 있는 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-330">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="29e4f-331">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-331">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="29e4f-332">서버의 이전 배포가 앱을 다시 배포하기 전에 완전히 삭제되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-332">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="29e4f-333">명령 프롬프트에서 `dotnet nuget locals all --clear`를 실행하면 간편하게 패키지 캐시를 지울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-333">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="29e4f-334">[nuget.exe](https://www.nuget.org/downloads) 도구를 사용하고 명령 `nuget locals all -clear`를 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-334">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="29e4f-335">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29e4f-335">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29e4f-336">추가 자료</span><span class="sxs-lookup"><span data-stu-id="29e4f-336">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
