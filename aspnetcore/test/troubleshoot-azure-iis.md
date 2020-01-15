---
title: Azure App Service 및 IIS에 대 한 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core 앱의 Azure App Service 및 인터넷 정보 서비스 (IIS) 배포 문제를 진단 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/20/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: b0f5d44f153a095a6108a12ee91f4cc46fe0a0de
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829012"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="1b00e-103">Azure App Service 및 IIS에 대 한 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="1b00e-104">By [Luke Latham 문자](https://github.com/guardrex) 및 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="1b00e-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="1b00e-105">이 문서에서는 일반적인 앱 시작 오류에 대 한 정보와 앱이 Azure App Service 또는 IIS에 배포 될 때 오류를 진단 하는 방법에 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="1b00e-106">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="1b00e-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="1b00e-107">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="1b00e-108">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="1b00e-109">Azure App Service에 배포 된 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="1b00e-110">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="1b00e-111">IIS에 배포 되었거나 로컬로 IIS Express에서 실행 되는 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="1b00e-112">지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="1b00e-113">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="1b00e-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="1b00e-114">Incoherent 패키지가 주요 업그레이드를 수행 하거나 패키지 버전을 변경할 때 앱이 중단 되는 경우 수행할 작업을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="1b00e-115">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="1b00e-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="1b00e-116">추가 문제 해결 항목을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="1b00e-117">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="1b00e-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1b00e-118">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="1b00e-119">이 항목의 지침을 사용 하 여 로컬로 디버그할 수 있는 경우 발생 하는 *502.5-프로세스 오류* 또는 *500.30 시작 오류* 입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1b00e-120">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="1b00e-121">로컬로 디버그할 때 발생 하는 *502.5 프로세스 오류* 는이 항목의 지침을 사용 하 여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="1b00e-122">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="1b00e-122">403.14 Forbidden</span></span>

<span data-ttu-id="1b00e-123">앱이 시작 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-123">The app fails to start.</span></span> <span data-ttu-id="1b00e-124">다음 오류가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="1b00e-125">오류는 일반적으로 다음과 같은 시나리오를 포함 하 여 호스팅 시스템의 끊어진 배포로 인해 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="1b00e-126">응용 프로그램이 호스팅 시스템의 잘못 된 폴더에 배포 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="1b00e-127">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="1b00e-128">Web.config *파일이 배포* 에 없거나 *web.config 파일 내용의* 형식이 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="1b00e-129">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-129">Perform the following steps:</span></span>

1. <span data-ttu-id="1b00e-130">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="1b00e-131">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용 하 여 앱의 *게시* 폴더 내용을 호스팅 시스템에 다시 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="1b00e-132">*Web.config* 파일이 배포에 있고 해당 콘텐츠가 올바른지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="1b00e-133">Azure App Service에서 호스팅할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="1b00e-134">IIS에서 응용 프로그램을 호스트 하는 경우 iis **관리자**의 **기본 설정**에 표시 된 iis **실제 경로** 에 앱이 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="1b00e-135">호스팅 시스템의 배포를 프로젝트의 *게시* 폴더의 콘텐츠와 비교 하 여 모든 앱의 파일 및 폴더가 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="1b00e-136">게시 된 ASP.NET Core 앱의 레이아웃에 대 한 자세한 내용은 <xref:host-and-deploy/directory-structure>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="1b00e-137">*Web.config 파일에* 대 한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="1b00e-138">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="1b00e-138">500 Internal Server Error</span></span>

<span data-ttu-id="1b00e-139">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="1b00e-140">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="1b00e-141">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="1b00e-142">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="1b00e-143">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="1b00e-144">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="1b00e-145">서버의 명령 프롬프트에서 앱을 실행 하거나 ASP.NET Core 모듈 stdout 로그를 사용 하 여 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="1b00e-146">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="1b00e-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="1b00e-147">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-147">The worker process fails.</span></span> <span data-ttu-id="1b00e-148">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-148">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-149">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) .NET Core CLR을 찾지 못하며 in-process 요청 처리기 (*aspnetcorev2_inprocess*)를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="1b00e-150">다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-150">Check that:</span></span>

* <span data-ttu-id="1b00e-151">앱은 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 패키지 또는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 중 하나를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="1b00e-152">앱이 대상으로 하는 ASP.NET Core 공유 프레임워크의 버전이 대상 머신에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="1b00e-153">500.0 Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="1b00e-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="1b00e-154">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-154">The worker process fails.</span></span> <span data-ttu-id="1b00e-155">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-155">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-156">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) out-of-process 호스팅 요청 처리기를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="1b00e-157">*aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 옆의 하위 폴더에 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="1b00e-158">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="1b00e-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="1b00e-159">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-159">The worker process fails.</span></span> <span data-ttu-id="1b00e-160">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-160">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-161">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 구성 요소를 로드 하는 동안 알 수 없는 오류가 발생 했습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="1b00e-162">다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-162">Take one of the following actions:</span></span>

* <span data-ttu-id="1b00e-163">[Microsoft 지원](https://support.microsoft.com/oas/default.aspx?prid=15832)에 문의하세요(**개발자 도구**를 선택한 다음, **ASP.NET Core** 선택).</span><span class="sxs-lookup"><span data-stu-id="1b00e-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="1b00e-164">Stack Overflow에 대해 질문하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="1b00e-165">[GitHub 리포지토리](https://github.com/dotnet/AspNetCore)에 문제를 제기하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-165">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="1b00e-166">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="1b00e-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="1b00e-167">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-167">The worker process fails.</span></span> <span data-ttu-id="1b00e-168">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-168">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-169">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) .NET Core CLR in process를 시작 하려고 하지만 시작 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="1b00e-170">프로세스 시작 오류의 원인은 일반적으로 응용 프로그램 이벤트 로그의 항목 및 ASP.NET Core 모듈 stdout 로그에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="1b00e-171">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-171">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="1b00e-172">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-172">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="1b00e-173">500.31 ANCM 네이티브 종속성을 찾지 못함</span><span class="sxs-lookup"><span data-stu-id="1b00e-173">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="1b00e-174">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-174">The worker process fails.</span></span> <span data-ttu-id="1b00e-175">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-175">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-176">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) .net Core 런타임을 in-process로 시작 하려고 했지만 시작 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-176">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="1b00e-177">이 시작 오류의 가장 일반적인 원인은 `Microsoft.NETCore.App` 또는 `Microsoft.AspNetCore.App` 런타임이 설치되어 있지 않은 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-177">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="1b00e-178">앱이 대상 ASP.NET Core 3.0에 배포되고 해당 버전이 머신에 없는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-178">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="1b00e-179">예제 오류 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-179">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="1b00e-180">오류 메시지는 설치된 모든 .NET Core 버전과 앱에서 요청한 버전을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-180">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="1b00e-181">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-181">To fix this error, either:</span></span>

* <span data-ttu-id="1b00e-182">머신에 적절한 버전의 .NET Core를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-182">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="1b00e-183">머신에 있는 .NET Core 버전을 대상으로 앱을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-183">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="1b00e-184">[자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-184">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="1b00e-185">개발 중에 실행될 때(`ASPNETCORE_ENVIRONMENT` 환경 변수가 `Development`로 설정됨) 특정 오류가 HTTP 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-185">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="1b00e-186">프로세스 시작 오류의 원인이 응용 프로그램 이벤트 로그에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-186">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="1b00e-187">500.32 ANCM dll을 로드하지 못함</span><span class="sxs-lookup"><span data-stu-id="1b00e-187">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="1b00e-188">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-188">The worker process fails.</span></span> <span data-ttu-id="1b00e-189">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-189">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-190">이 오류의 가장 일반적인 원인은 앱이 호환되지 않는 프로세서 아키텍처에 대해 게시되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-190">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="1b00e-191">작업자 프로세스가 32비트 앱으로 실행 중이고 해당 앱이 대상 64 비트로 게시된 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-191">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="1b00e-192">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-192">To fix this error, either:</span></span>

* <span data-ttu-id="1b00e-193">작업자 프로세스와 동일한 프로세서 아키텍처에 대해 앱을 다시 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-193">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="1b00e-194">앱을 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-executables-fde)로 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-194">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="1b00e-195">500.33 ANCM 요청 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="1b00e-195">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="1b00e-196">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-196">The worker process fails.</span></span> <span data-ttu-id="1b00e-197">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-197">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-198">앱이 `Microsoft.AspNetCore.App` 프레임워크를 참조하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-198">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="1b00e-199">`Microsoft.AspNetCore.App` 프레임 워크를 대상으로 하는 앱만 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)에서 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-199">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="1b00e-200">이 오류를 해결하려면 앱이 `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-200">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="1b00e-201">`.runtimeconfig.json`을 확인하여 앱이 대상으로 하는 프레임워크를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-201">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="1b00e-202">500.34 ANCM 혼합된 호스팅 모델이 지원되지 않음</span><span class="sxs-lookup"><span data-stu-id="1b00e-202">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="1b00e-203">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-203">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="1b00e-204">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-204">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="1b00e-205">500.35 ANCM 동일한 프로세스의 여러 In-Process 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="1b00e-205">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="1b00e-206">작업자 프로세스는 동일한 프로세스에서 여러 in-process 앱을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-206">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="1b00e-207">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="1b00e-208">500.36 ANCM Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="1b00e-208">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="1b00e-209">Out-of-process 요청 처리기, *aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 파일 옆에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-209">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="1b00e-210">이는 [ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module)손상 되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-210">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="1b00e-211">이 오류를 해결하려면 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)(IIS의 경우) 또는 Visual Studio(IIS Express의 경우)의 설치를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-211">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="1b00e-212">500.37 ANCM 시작 시간 제한 내에 시작하지 못함</span><span class="sxs-lookup"><span data-stu-id="1b00e-212">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="1b00e-213">ANCM은 제공된 시작 시간 제한 내에 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-213">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="1b00e-214">기본적으로 제한 시간은 120초입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-214">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="1b00e-215">이 오류는 동일한 머신에서 많은 수의 앱을 시작할 때 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-215">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="1b00e-216">시작하는 동안 서버에서 CPU/메모리 사용량이 급증하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-216">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="1b00e-217">여러 앱의 시작 프로세스를 분산해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-217">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="1b00e-218">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="1b00e-218">502.5 Process Failure</span></span>

<span data-ttu-id="1b00e-219">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-219">The worker process fails.</span></span> <span data-ttu-id="1b00e-220">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-220">The app doesn't start.</span></span>

<span data-ttu-id="1b00e-221">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-221">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="1b00e-222">프로세스 시작 오류의 원인은 일반적으로 응용 프로그램 이벤트 로그의 항목 및 ASP.NET Core 모듈 stdout 로그에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-222">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="1b00e-223">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-223">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="1b00e-224">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-224">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="1b00e-225">*공유 프레임워크*는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-225">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="1b00e-226">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-226">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="1b00e-227">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-227">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="1b00e-228">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-228">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="1b00e-229">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="1b00e-229">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="1b00e-230">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-230">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="1b00e-231">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-231">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="1b00e-232">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-232">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="1b00e-233">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-233">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="1b00e-234">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-234">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="1b00e-235">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-235">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="1b00e-236">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-236">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="1b00e-237">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-237">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="1b00e-238">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시 된 비트 간에 충돌이 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-238">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="1b00e-239">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="1b00e-239">Connection reset</span></span>

<span data-ttu-id="1b00e-240">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-240">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="1b00e-241">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-241">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="1b00e-242">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-242">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="1b00e-243">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-243">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="1b00e-244">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="1b00e-244">Default startup limits</span></span>

<span data-ttu-id="1b00e-245">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 은 기본 *startupTimeLimit* 120 초를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-245">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="1b00e-246">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-246">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="1b00e-247">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-247">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="1b00e-248">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-248">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="1b00e-249">응용 프로그램 이벤트 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="1b00e-249">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="1b00e-250">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-250">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="1b00e-251">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-251">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="1b00e-252">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-252">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="1b00e-253">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-253">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="1b00e-254">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-254">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="1b00e-255">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-255">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="1b00e-256">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-256">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="1b00e-257">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="1b00e-258">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="1b00e-259">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="1b00e-260">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="1b00e-261">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-261">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="1b00e-262">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-262">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="1b00e-263">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-263">Examine the log.</span></span> <span data-ttu-id="1b00e-264">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-264">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="1b00e-265">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="1b00e-265">Run the app in the Kudu console</span></span>

<span data-ttu-id="1b00e-266">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-266">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="1b00e-267">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-267">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="1b00e-268">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-268">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="1b00e-269">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-269">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="1b00e-270">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-270">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="1b00e-271">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-271">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="1b00e-272">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="1b00e-272">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="1b00e-273">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="1b00e-273">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="1b00e-274">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-274">Run the app:</span></span>
   * <span data-ttu-id="1b00e-275">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="1b00e-275">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="1b00e-276">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="1b00e-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="1b00e-277">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-277">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="1b00e-278">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="1b00e-278">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="1b00e-279">*ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.*</span><span class="sxs-lookup"><span data-stu-id="1b00e-279">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="1b00e-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="1b00e-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="1b00e-281">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="1b00e-281">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="1b00e-282">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-282">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="1b00e-283">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="1b00e-283">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="1b00e-284">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="1b00e-284">**Current release**</span></span>

* <span data-ttu-id="1b00e-285">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="1b00e-285">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="1b00e-286">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="1b00e-286">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="1b00e-287">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="1b00e-287">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="1b00e-288">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="1b00e-288">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="1b00e-289">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-289">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="1b00e-290">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="1b00e-290">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="1b00e-291">*ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.*</span><span class="sxs-lookup"><span data-stu-id="1b00e-291">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="1b00e-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="1b00e-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="1b00e-293">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="1b00e-293">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="1b00e-294">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-294">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="1b00e-295">ASP.NET Core 모듈 stdout 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="1b00e-295">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="1b00e-296">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-296">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="1b00e-297">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="1b00e-297">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="1b00e-298">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-298">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="1b00e-299">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-299">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="1b00e-300">**추천 솔루션** > **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-300">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="1b00e-301">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-301">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="1b00e-302">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-302">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="1b00e-303">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-303">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="1b00e-304">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-304">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="1b00e-305">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-305">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="1b00e-306">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-306">Make a request to the app.</span></span>
1. <span data-ttu-id="1b00e-307">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-307">Return to the Azure portal.</span></span> <span data-ttu-id="1b00e-308">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-308">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="1b00e-309">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-309">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="1b00e-310">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-310">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="1b00e-311">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-311">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="1b00e-312">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-312">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="1b00e-313">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-313">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="1b00e-314">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-314">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="1b00e-315">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-315">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="1b00e-316">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-316">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="1b00e-317">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-317">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="1b00e-318">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-318">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="1b00e-319">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-319">Select **Save** to save the file.</span></span>

<span data-ttu-id="1b00e-320">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-320">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="1b00e-321">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-321">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="1b00e-322">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-322">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="1b00e-323">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-323">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="1b00e-324">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-324">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1b00e-325">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-325">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="1b00e-326">ASP.NET Core 모듈 디버그 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="1b00e-326">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="1b00e-327">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-327">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="1b00e-328">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="1b00e-328">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="1b00e-329">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-329">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="1b00e-330">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-330">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="1b00e-331">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-331">Redeploy the app.</span></span>
   * <span data-ttu-id="1b00e-332">Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-332">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="1b00e-333">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-333">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="1b00e-334">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-334">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="1b00e-335">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-335">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="1b00e-336">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-336">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="1b00e-337">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-337">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="1b00e-338">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-338">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="1b00e-339">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-339">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="1b00e-340">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-340">Select the **Save** button.</span></span>
1. <span data-ttu-id="1b00e-341">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-341">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="1b00e-342">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-342">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="1b00e-343">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-343">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="1b00e-344">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-344">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="1b00e-345">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-345">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="1b00e-346">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-346">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="1b00e-347">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-347">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="1b00e-348">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-348">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="1b00e-349">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-349">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="1b00e-350">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-350">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="1b00e-351">*web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-351">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="1b00e-352">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-352">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="1b00e-353">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-353">Save the file.</span></span>

<span data-ttu-id="1b00e-354">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-354">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="1b00e-355">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-355">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="1b00e-356">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-356">There's no limit on log file size.</span></span> <span data-ttu-id="1b00e-357">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-357">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="1b00e-358">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-358">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1b00e-359">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-359">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="1b00e-360">느리거나 중단 앱 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="1b00e-360">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="1b00e-361">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-361">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="1b00e-362">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-362">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="1b00e-363">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="1b00e-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="1b00e-364">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="1b00e-364">Monitoring blades</span></span>

<span data-ttu-id="1b00e-365">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-365">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="1b00e-366">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-366">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="1b00e-367">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-367">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="1b00e-368">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-368">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="1b00e-369">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-369">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="1b00e-370">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-370">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="1b00e-371">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-371">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="1b00e-372">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-372">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="1b00e-373">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-373">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="1b00e-374">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-374">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="1b00e-375">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-375">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="1b00e-376">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-376">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="1b00e-377">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-377">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="1b00e-378">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-378">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="1b00e-379">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-379">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="1b00e-380">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-380">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="1b00e-381">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-381">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="1b00e-382">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-382">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="1b00e-383">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-383">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="1b00e-384">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-384">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="1b00e-385">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-385">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="1b00e-386">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-386">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="1b00e-387">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-387">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="1b00e-388">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-388">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="1b00e-389">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-389">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="1b00e-390">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-390">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="1b00e-391">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-391">Make a request to the app.</span></span>
1. <span data-ttu-id="1b00e-392">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-392">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="1b00e-393">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-393">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="1b00e-394">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="1b00e-394">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="1b00e-395">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-395">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="1b00e-396">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-396">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="1b00e-397">[Azure App Service에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ: 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-397">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="1b00e-398">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-398">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="1b00e-399">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-399">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="1b00e-400">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-400">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="1b00e-401">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-401">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1b00e-402">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-402">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="1b00e-403">IIS에 대 한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-403">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="1b00e-404">응용 프로그램 이벤트 로그 (IIS)</span><span class="sxs-lookup"><span data-stu-id="1b00e-404">Application Event Log (IIS)</span></span>

<span data-ttu-id="1b00e-405">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-405">Access the Application Event Log:</span></span>

1. <span data-ttu-id="1b00e-406">[시작] 메뉴를 열고 **이벤트 뷰어**를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-406">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1b00e-407">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-407">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1b00e-408">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-408">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1b00e-409">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-409">Search for errors associated with the failing app.</span></span> <span data-ttu-id="1b00e-410">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-410">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1b00e-411">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="1b00e-411">Run the app at a command prompt</span></span>

<span data-ttu-id="1b00e-412">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-412">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="1b00e-413">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-413">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="1b00e-414">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="1b00e-414">Framework-dependent deployment</span></span>

<span data-ttu-id="1b00e-415">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="1b00e-415">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="1b00e-416">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-416">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="1b00e-417">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-417">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="1b00e-418">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-418">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="1b00e-419">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-419">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="1b00e-420">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-420">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="1b00e-421">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-421">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="1b00e-422">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="1b00e-422">Self-contained deployment</span></span>

<span data-ttu-id="1b00e-423">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="1b00e-423">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="1b00e-424">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-424">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="1b00e-425">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-425">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="1b00e-426">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-426">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="1b00e-427">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-427">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="1b00e-428">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-428">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="1b00e-429">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-429">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="1b00e-430">ASP.NET Core 모듈 stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="1b00e-430">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="1b00e-431">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="1b00e-431">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="1b00e-432">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-432">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="1b00e-433">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-433">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="1b00e-434">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-434">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="1b00e-435">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-435">Edit the *web.config* file.</span></span> <span data-ttu-id="1b00e-436">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="1b00e-436">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="1b00e-437">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-437">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="1b00e-438">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-438">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="1b00e-439">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-439">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="1b00e-440">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-440">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="1b00e-441">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-441">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="1b00e-442">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-442">Make a request to the app.</span></span>
1. <span data-ttu-id="1b00e-443">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-443">Navigate to the *logs* folder.</span></span> <span data-ttu-id="1b00e-444">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-444">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="1b00e-445">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-445">Study the log for errors.</span></span>

<span data-ttu-id="1b00e-446">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-446">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="1b00e-447">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-447">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="1b00e-448">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-448">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="1b00e-449">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-449">Save the file.</span></span>

<span data-ttu-id="1b00e-450">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-450">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="1b00e-451">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-451">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="1b00e-452">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-452">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="1b00e-453">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-453">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1b00e-454">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-454">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="1b00e-455">ASP.NET Core 모듈 디버그 로그 (IIS)</span><span class="sxs-lookup"><span data-stu-id="1b00e-455">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="1b00e-456">응용 프로그램의 *web.config* 파일에 다음 처리기 설정을 추가 하 여 ASP.NET Core 모듈 디버그 로그를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-456">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="1b00e-457">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-457">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="1b00e-458">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-458">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="1b00e-459">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="1b00e-459">Enable the Developer Exception Page</span></span>

<span data-ttu-id="1b00e-460">`ASPNETCORE_ENVIRONMENT` [환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 하 여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-460">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="1b00e-461">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-461">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="1b00e-462">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-462">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="1b00e-463">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-463">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="1b00e-464">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-464">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="1b00e-465">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="1b00e-465">Obtain data from an app</span></span>

<span data-ttu-id="1b00e-466">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-466">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="1b00e-467">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-467">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="1b00e-468">느리거나 중단 앱 (IIS)</span><span class="sxs-lookup"><span data-stu-id="1b00e-468">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="1b00e-469">*크래시 덤프* 는 시스템 메모리의 스냅숏이 며 앱 충돌, 시작 오류 또는 느림 앱의 원인을 확인 하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-469">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1b00e-470">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="1b00e-470">App crashes or encounters an exception</span></span>

<span data-ttu-id="1b00e-471">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-471">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1b00e-472">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-472">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="1b00e-473">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-473">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="1b00e-474">[EnableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="1b00e-474">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="1b00e-475">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="1b00e-475">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="1b00e-476">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="1b00e-476">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="1b00e-477">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-477">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1b00e-478">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="1b00e-478">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="1b00e-479">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="1b00e-479">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="1b00e-480">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="1b00e-480">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="1b00e-481">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-481">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1b00e-482">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-482">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1b00e-483">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-483">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1b00e-484">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="1b00e-484">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1b00e-485">*앱이 중지 되* 는 경우 (응답을 중지 하지만 충돌 하지 않음), 시작 중에 실패 하거나 정상적으로 실행 되는 경우 [사용자 모드 덤프 파일: 가장](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 적합 한 도구를 선택 하 여 덤프를 생성 하는 데 적합 한 도구 선택을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-485">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1b00e-486">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="1b00e-486">Analyze the dump</span></span>

<span data-ttu-id="1b00e-487">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-487">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1b00e-488">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1b00e-488">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="1b00e-489">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="1b00e-489">Clear package caches</span></span>

<span data-ttu-id="1b00e-490">경우에 따라 개발 컴퓨터에서 .NET Core SDK를 업그레이드 하거나 앱 내에서 패키지 버전을 변경 하는 즉시 작동 하는 앱이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-490">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1b00e-491">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-491">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1b00e-492">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-492">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1b00e-493">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-493">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1b00e-494">명령 셸에서 `dotnet nuget locals all --clear`를 실행 하 여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-494">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span></span>

   <span data-ttu-id="1b00e-495">[Nuget](https://www.nuget.org/downloads) 도구를 사용 하 여 패키지 캐시를 지우면 서 `nuget locals all -clear`명령을 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-495">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1b00e-496">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-496">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1b00e-497">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-497">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1b00e-498">앱을 다시 배포 하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="1b00e-498">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b00e-499">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1b00e-499">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="1b00e-500">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="1b00e-500">Azure documentation</span></span>

* [<span data-ttu-id="1b00e-501">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="1b00e-501">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="1b00e-502">Visual Studio를 사용 하 여 Azure App Service에서 웹 앱 문제 해결의 원격 디버깅 웹 앱 섹션</span><span class="sxs-lookup"><span data-stu-id="1b00e-502">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="1b00e-503">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="1b00e-503">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="1b00e-504">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="1b00e-504">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="1b00e-505">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-505">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="1b00e-506">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-506">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="1b00e-507">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="1b00e-507">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="1b00e-508">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="1b00e-508">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="1b00e-509">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="1b00e-509">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="1b00e-510">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 비디오)</span><span class="sxs-lookup"><span data-stu-id="1b00e-510">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="1b00e-511">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="1b00e-511">Visual Studio documentation</span></span>

* [<span data-ttu-id="1b00e-512">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b00e-512">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="1b00e-513">Visual Studio 2017 원격 IIS 컴퓨터의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b00e-513">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="1b00e-514">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="1b00e-514">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="1b00e-515">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="1b00e-515">Visual Studio Code documentation</span></span>

* <span data-ttu-id="1b00e-516">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="1b00e-516">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>
