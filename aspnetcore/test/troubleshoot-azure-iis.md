---
title: Azure App Service 및 IIS에 대 한 ASP.NET Core 문제 해결
author: guardrex
description: ASP.NET Core 앱의 Azure App Service 및 인터넷 정보 서비스 (IIS) 배포 문제를 진단 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: a5cd17e46126828c6bc8436ccaaca28edb2573d0
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114850"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="48d29-103">Azure App Service 및 IIS에 대 한 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="48d29-104">By [Luke Latham 문자](https://github.com/guardrex) 및 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="48d29-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48d29-105">이 문서에서는 일반적인 앱 시작 오류에 대 한 정보와 앱이 Azure App Service 또는 IIS에 배포 될 때 오류를 진단 하는 방법에 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="48d29-106">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="48d29-107">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="48d29-108">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="48d29-109">Azure App Service에 배포 된 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="48d29-110">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="48d29-111">IIS에 배포 되었거나 로컬로 IIS Express에서 실행 되는 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="48d29-112">지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="48d29-113">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="48d29-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="48d29-114">Incoherent 패키지가 주요 업그레이드를 수행 하거나 패키지 버전을 변경할 때 앱이 중단 되는 경우 수행할 작업을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="48d29-115">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="48d29-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="48d29-116">추가 문제 해결 항목을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="48d29-117">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-117">App startup errors</span></span>

<span data-ttu-id="48d29-118">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="48d29-119">이 항목의 지침을 사용 하 여 로컬로 디버그할 수 있는 경우 발생 하는 *502.5-프로세스 오류* 또는 *500.30 시작 오류* 입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="48d29-120">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="48d29-120">403.14 Forbidden</span></span>

<span data-ttu-id="48d29-121">앱이 시작 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-121">The app fails to start.</span></span> <span data-ttu-id="48d29-122">다음 오류가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="48d29-123">오류는 일반적으로 다음과 같은 시나리오를 포함 하 여 호스팅 시스템의 끊어진 배포로 인해 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="48d29-124">응용 프로그램이 호스팅 시스템의 잘못 된 폴더에 배포 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="48d29-125">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="48d29-126">Web.config *파일이 배포* 에 없거나 *web.config 파일 내용의* 형식이 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="48d29-127">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-127">Perform the following steps:</span></span>

1. <span data-ttu-id="48d29-128">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="48d29-129">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용 하 여 앱의 *게시* 폴더 내용을 호스팅 시스템에 다시 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="48d29-130">*Web.config* 파일이 배포에 있고 해당 콘텐츠가 올바른지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="48d29-131">Azure App Service에서 호스팅할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="48d29-132">IIS에서 응용 프로그램을 호스트 하는 경우 iis **관리자**의 **기본 설정**에 표시 된 iis **실제 경로** 에 앱이 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="48d29-133">호스팅 시스템의 배포를 프로젝트의 *게시* 폴더의 콘텐츠와 비교 하 여 모든 앱의 파일 및 폴더가 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="48d29-134">게시 된 ASP.NET Core 앱의 레이아웃에 대 한 자세한 내용은 <xref:host-and-deploy/directory-structure>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="48d29-135">*Web.config 파일에* 대 한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="48d29-136">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-136">500 Internal Server Error</span></span>

<span data-ttu-id="48d29-137">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="48d29-138">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="48d29-139">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="48d29-140">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="48d29-141">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="48d29-142">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="48d29-143">서버의 명령 프롬프트에서 앱을 실행 하거나 ASP.NET Core 모듈 stdout 로그를 사용 하 여 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="48d29-144">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="48d29-145">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-145">The worker process fails.</span></span> <span data-ttu-id="48d29-146">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-146">The app doesn't start.</span></span>

<span data-ttu-id="48d29-147">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 구성 요소를 로드 하는 동안 알 수 없는 오류가 발생 했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="48d29-148">다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-148">Take one of the following actions:</span></span>

* <span data-ttu-id="48d29-149">[Microsoft 지원](https://support.microsoft.com/oas/default.aspx?prid=15832)에 문의하세요(**개발자 도구**를 선택한 다음, **ASP.NET Core** 선택).</span><span class="sxs-lookup"><span data-stu-id="48d29-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="48d29-150">Stack Overflow에 대해 질문하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="48d29-151">[GitHub 리포지토리](https://github.com/dotnet/AspNetCore)에 문제를 제기하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="48d29-152">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="48d29-153">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-153">The worker process fails.</span></span> <span data-ttu-id="48d29-154">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-154">The app doesn't start.</span></span>

<span data-ttu-id="48d29-155">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) .NET Core CLR in process를 시작 하려고 하지만 시작 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="48d29-156">프로세스 시작 오류의 원인은 일반적으로 응용 프로그램 이벤트 로그의 항목 및 ASP.NET Core 모듈 stdout 로그에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="48d29-157">일반적인 오류 조건:</span><span class="sxs-lookup"><span data-stu-id="48d29-157">Common failure conditions:</span></span>

* <span data-ttu-id="48d29-158">존재 하지 않는 ASP.NET Core 공유 프레임 워크의 버전을 대상으로 지정 했기 때문에 앱이 잘못 구성 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="48d29-159">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="48d29-160">Azure Key Vault를 사용 하 여 Key Vault에 대 한 사용 권한이 부족 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="48d29-161">대상 Key Vault의 액세스 정책을 확인 하 여 올바른 사용 권한이 부여 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="48d29-162">500.31 ANCM 네이티브 종속성을 찾지 못함</span><span class="sxs-lookup"><span data-stu-id="48d29-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="48d29-163">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-163">The worker process fails.</span></span> <span data-ttu-id="48d29-164">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-164">The app doesn't start.</span></span>

<span data-ttu-id="48d29-165">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) .net Core 런타임을 in-process로 시작 하려고 했지만 시작 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="48d29-166">이 시작 오류의 가장 일반적인 원인은 `Microsoft.NETCore.App` 또는 `Microsoft.AspNetCore.App` 런타임이 설치되어 있지 않은 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="48d29-167">앱이 대상 ASP.NET Core 3.0에 배포되고 해당 버전이 머신에 없는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="48d29-168">예제 오류 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="48d29-169">오류 메시지는 설치된 모든 .NET Core 버전과 앱에서 요청한 버전을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="48d29-170">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-170">To fix this error, either:</span></span>

* <span data-ttu-id="48d29-171">머신에 적절한 버전의 .NET Core를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="48d29-172">머신에 있는 .NET Core 버전을 대상으로 앱을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="48d29-173">[자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="48d29-174">개발 중에 실행될 때(`ASPNETCORE_ENVIRONMENT` 환경 변수가 `Development`로 설정됨) 특정 오류가 HTTP 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="48d29-175">프로세스 시작 오류의 원인이 응용 프로그램 이벤트 로그에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="48d29-176">500.32 ANCM dll을 로드하지 못함</span><span class="sxs-lookup"><span data-stu-id="48d29-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="48d29-177">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-177">The worker process fails.</span></span> <span data-ttu-id="48d29-178">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-178">The app doesn't start.</span></span>

<span data-ttu-id="48d29-179">이 오류의 가장 일반적인 원인은 앱이 호환되지 않는 프로세서 아키텍처에 대해 게시되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="48d29-180">작업자 프로세스가 32비트 앱으로 실행 중이고 해당 앱이 대상 64 비트로 게시된 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="48d29-181">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-181">To fix this error, either:</span></span>

* <span data-ttu-id="48d29-182">작업자 프로세스와 동일한 프로세서 아키텍처에 대해 앱을 다시 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="48d29-183">앱을 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-executables-fde)로 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="48d29-184">500.33 ANCM 요청 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="48d29-185">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-185">The worker process fails.</span></span> <span data-ttu-id="48d29-186">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-186">The app doesn't start.</span></span>

<span data-ttu-id="48d29-187">앱이 `Microsoft.AspNetCore.App` 프레임워크를 참조하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="48d29-188">`Microsoft.AspNetCore.App` 프레임 워크를 대상으로 하는 앱만 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)에서 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="48d29-189">이 오류를 해결하려면 앱이 `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="48d29-190">`.runtimeconfig.json`을 확인하여 앱이 대상으로 하는 프레임워크를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="48d29-191">500.34 ANCM 혼합된 호스팅 모델이 지원되지 않음</span><span class="sxs-lookup"><span data-stu-id="48d29-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="48d29-192">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="48d29-193">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="48d29-194">500.35 ANCM 동일한 프로세스의 여러 In-Process 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="48d29-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="48d29-195">작업자 프로세스는 동일한 프로세스에서 여러 in-process 앱을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="48d29-196">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="48d29-197">500.36 ANCM Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="48d29-198">Out-of-process 요청 처리기, *aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 파일 옆에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="48d29-199">이는 [ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module)손상 되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="48d29-200">이 오류를 해결하려면 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)(IIS의 경우) 또는 Visual Studio(IIS Express의 경우)의 설치를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="48d29-201">500.37 ANCM 시작 시간 제한 내에 시작하지 못함</span><span class="sxs-lookup"><span data-stu-id="48d29-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="48d29-202">ANCM은 제공된 시작 시간 제한 내에 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="48d29-203">기본적으로 제한 시간은 120초입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="48d29-204">이 오류는 동일한 머신에서 많은 수의 앱을 시작할 때 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="48d29-205">시작하는 동안 서버에서 CPU/메모리 사용량이 급증하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="48d29-206">여러 앱의 시작 프로세스를 분산해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="48d29-207">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-207">502.5 Process Failure</span></span>

<span data-ttu-id="48d29-208">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-208">The worker process fails.</span></span> <span data-ttu-id="48d29-209">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-209">The app doesn't start.</span></span>

<span data-ttu-id="48d29-210">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="48d29-211">프로세스 시작 오류의 원인은 일반적으로 응용 프로그램 이벤트 로그의 항목 및 ASP.NET Core 모듈 stdout 로그에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-211">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="48d29-212">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-212">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="48d29-213">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-213">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="48d29-214">*공유 프레임워크*는 머신에 설치되고 메타패키지(예: *)에서 참조되는 어셈블리(* .dll`Microsoft.AspNetCore.App` 파일) 세트입니다.{6}{7}</span><span class="sxs-lookup"><span data-stu-id="48d29-214">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="48d29-215">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-215">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="48d29-216">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-216">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="48d29-217">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-217">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="48d29-218">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="48d29-218">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="48d29-219">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-219">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="48d29-220">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-220">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="48d29-221">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-221">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="48d29-222">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-222">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="48d29-223">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-223">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="48d29-224">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-224">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="48d29-225">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-225">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="48d29-226">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-226">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="48d29-227">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시 된 비트 간에 충돌이 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-227">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="48d29-228">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="48d29-228">Connection reset</span></span>

<span data-ttu-id="48d29-229">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-229">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="48d29-230">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-230">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="48d29-231">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-231">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="48d29-232">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-232">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="48d29-233">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="48d29-233">Default startup limits</span></span>

<span data-ttu-id="48d29-234">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 은 기본 *startupTimeLimit* 120 초를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-234">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="48d29-235">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-235">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="48d29-236">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-236">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="48d29-237">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-237">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="48d29-238">응용 프로그램 이벤트 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-238">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="48d29-239">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-239">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="48d29-240">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-240">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="48d29-241">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-241">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="48d29-242">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-242">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="48d29-243">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-243">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="48d29-244">*원본* 열에서 *IIS AspNetCoreModule* 또는 **IIS AspNetCoreModule V2** 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-244">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="48d29-245">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-245">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="48d29-246">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-246">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-247">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-247">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-248">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-248">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-249">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-249">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-250">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-250">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="48d29-251">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-251">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="48d29-252">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-252">Examine the log.</span></span> <span data-ttu-id="48d29-253">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-253">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="48d29-254">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-254">Run the app in the Kudu console</span></span>

<span data-ttu-id="48d29-255">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-255">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="48d29-256">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-256">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="48d29-257">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-258">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-259">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-260">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="48d29-261">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="48d29-261">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="48d29-262">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="48d29-262">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="48d29-263">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-263">Run the app:</span></span>
   * <span data-ttu-id="48d29-264">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-264">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="48d29-265">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-265">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="48d29-266">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-266">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="48d29-267">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="48d29-267">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="48d29-268">*ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.* {3}{4}</span><span class="sxs-lookup"><span data-stu-id="48d29-268">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="48d29-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="48d29-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="48d29-270">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-270">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="48d29-271">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-271">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="48d29-272">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="48d29-272">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="48d29-273">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="48d29-273">**Current release**</span></span>

* <span data-ttu-id="48d29-274">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-274">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="48d29-275">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-275">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="48d29-276">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="48d29-277">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="48d29-277">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="48d29-278">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-278">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="48d29-279">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="48d29-279">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="48d29-280">*ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.* {3}{4}</span><span class="sxs-lookup"><span data-stu-id="48d29-280">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="48d29-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="48d29-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="48d29-282">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-282">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="48d29-283">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-283">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="48d29-284">ASP.NET Core 모듈 stdout 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-284">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="48d29-285">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-285">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="48d29-286">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-286">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-287">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-287">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="48d29-288">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-288">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="48d29-289">**권장 해결 방법** > **Stdout 로그 리디렉션 사용**에서 단추를 선택 하 여 **Kudu 콘솔을 열고 web.config를 편집**합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-289">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="48d29-290">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-290">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="48d29-291">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-291">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="48d29-292">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-292">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-293">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-293">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="48d29-294">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-294">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="48d29-295">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-295">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-296">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-296">Return to the Azure portal.</span></span> <span data-ttu-id="48d29-297">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-297">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="48d29-298">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-298">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-299">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-299">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-300">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-300">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-301">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-301">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="48d29-302">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-302">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="48d29-303">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-303">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="48d29-304">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-304">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="48d29-305">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-305">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="48d29-306">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-306">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="48d29-307">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-307">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="48d29-308">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-308">Select **Save** to save the file.</span></span>

<span data-ttu-id="48d29-309">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-309">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-310">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-310">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-311">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-311">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="48d29-312">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-312">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="48d29-313">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-313">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-314">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-314">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="48d29-315">ASP.NET Core 모듈 디버그 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-315">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="48d29-316">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-316">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="48d29-317">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-317">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-318">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-318">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="48d29-319">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-319">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="48d29-320">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-320">Redeploy the app.</span></span>
   * <span data-ttu-id="48d29-321">Kudu 콘솔을 사용하여 `<handlerSettings>`개선된 진단 로그[에 표시되는 ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-321">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="48d29-322">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-322">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-323">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-323">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-324">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-324">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="48d29-325">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-325">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="48d29-326">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-326">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="48d29-327">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-327">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="48d29-328">`<handlerSettings>`개선된 진단 로그[에 표시된 대로 ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-328">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="48d29-329">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-329">Select the **Save** button.</span></span>
1. <span data-ttu-id="48d29-330">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-331">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-332">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-332">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-333">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-334">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="48d29-335">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-335">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="48d29-336">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-336">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="48d29-337">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-337">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="48d29-338">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-338">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="48d29-339">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-339">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="48d29-340">`<handlerSettings>`web.config*파일에서*를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-340">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="48d29-341">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-341">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="48d29-342">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-342">Save the file.</span></span>

<span data-ttu-id="48d29-343">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-343">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-344">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-344">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-345">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-345">There's no limit on log file size.</span></span> <span data-ttu-id="48d29-346">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-346">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="48d29-347">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-347">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-348">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-348">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="48d29-349">느리거나 중단 앱 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-349">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="48d29-350">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-350">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="48d29-351">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-351">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="48d29-352">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="48d29-352">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="48d29-353">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="48d29-353">Monitoring blades</span></span>

<span data-ttu-id="48d29-354">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-354">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="48d29-355">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-355">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="48d29-356">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-356">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="48d29-357">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-357">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="48d29-358">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-358">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="48d29-359">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-359">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="48d29-360">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-360">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="48d29-361">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-361">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="48d29-362">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-362">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="48d29-363">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-363">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="48d29-364">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-364">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="48d29-365">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-365">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="48d29-366">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-366">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="48d29-367">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-367">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-368">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-368">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-369">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-369">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-370">**Wwwroot** 의 경로 **사이트** > 폴더를 열고 아래로 스크롤하여 목록의 맨 아래에 있는 *web.config* 파일을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-370">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="48d29-371">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-371">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-372">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-372">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="48d29-373">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-373">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="48d29-374">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-374">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="48d29-375">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-375">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="48d29-376">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-376">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="48d29-377">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-377">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="48d29-378">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-378">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="48d29-379">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-379">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="48d29-380">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-380">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-381">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-381">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="48d29-382">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-382">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="48d29-383">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-383">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="48d29-384">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-384">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="48d29-385">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-385">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="48d29-386">[Azure App Service에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ: 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-386">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="48d29-387">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-387">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-388">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-388">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-389">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-389">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="48d29-390">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-390">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-391">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-391">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="48d29-392">IIS에 대 한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-392">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="48d29-393">응용 프로그램 이벤트 로그 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-393">Application Event Log (IIS)</span></span>

<span data-ttu-id="48d29-394">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-394">Access the Application Event Log:</span></span>

1. <span data-ttu-id="48d29-395">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-395">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="48d29-396">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-396">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="48d29-397">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-397">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="48d29-398">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-398">Search for errors associated with the failing app.</span></span> <span data-ttu-id="48d29-399">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-399">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="48d29-400">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-400">Run the app at a command prompt</span></span>

<span data-ttu-id="48d29-401">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-401">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="48d29-402">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-402">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="48d29-403">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="48d29-403">Framework-dependent deployment</span></span>

<span data-ttu-id="48d29-404">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-404">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="48d29-405">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-405">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="48d29-406">\< 명령에서 `dotnet .\<assembly_name>.dll`assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-406">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="48d29-407">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-407">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="48d29-408">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-408">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="48d29-409">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-409">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="48d29-410">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-410">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="48d29-411">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="48d29-411">Self-contained deployment</span></span>

<span data-ttu-id="48d29-412">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-412">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="48d29-413">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-413">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="48d29-414">\< 명령에서 `<assembly_name>.exe`assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="48d29-415">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="48d29-416">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="48d29-417">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="48d29-418">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="48d29-419">ASP.NET Core 모듈 stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-419">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="48d29-420">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-420">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-421">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-421">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="48d29-422">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-422">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="48d29-423">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-423">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="48d29-424">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-424">Edit the *web.config* file.</span></span> <span data-ttu-id="48d29-425">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="48d29-425">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="48d29-426">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-426">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="48d29-427">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-427">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="48d29-428">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-428">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="48d29-429">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-429">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="48d29-430">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-430">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="48d29-431">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-431">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-432">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-432">Navigate to the *logs* folder.</span></span> <span data-ttu-id="48d29-433">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-433">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="48d29-434">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-434">Study the log for errors.</span></span>

<span data-ttu-id="48d29-435">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-435">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="48d29-436">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-436">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-437">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-437">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="48d29-438">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-438">Save the file.</span></span>

<span data-ttu-id="48d29-439">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-439">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-440">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-440">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-441">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-441">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="48d29-442">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-442">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-443">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-443">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="48d29-444">ASP.NET Core 모듈 디버그 로그 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-444">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="48d29-445">응용 프로그램의 *web.config* 파일에 다음 처리기 설정을 추가 하 여 ASP.NET Core 모듈 디버그 로그를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-445">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="48d29-446">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-446">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="48d29-447">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-447">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="48d29-448">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="48d29-448">Enable the Developer Exception Page</span></span>

<span data-ttu-id="48d29-449">`ASPNETCORE_ENVIRONMENT` [환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 하 여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-449">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="48d29-450">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-450">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="48d29-451">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-451">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="48d29-452">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-452">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="48d29-453">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-453">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="48d29-454">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="48d29-454">Obtain data from an app</span></span>

<span data-ttu-id="48d29-455">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-455">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="48d29-456">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-456">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="48d29-457">느리거나 중단 앱 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-457">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="48d29-458">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-458">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="48d29-459">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="48d29-459">App crashes or encounters an exception</span></span>

<span data-ttu-id="48d29-460">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-460">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="48d29-461">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-461">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="48d29-462">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-462">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="48d29-463">[EnableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-463">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="48d29-464">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-464">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="48d29-465">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-465">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="48d29-466">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-466">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="48d29-467">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-467">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="48d29-468">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-468">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="48d29-469">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-469">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="48d29-470">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-470">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="48d29-471">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-471">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-472">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-472">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="48d29-473">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-473">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="48d29-474">*앱이 중지 되* 는 경우 (응답을 중지 하지만 충돌 하지 않음), 시작 중에 실패 하거나 정상적으로 실행 되는 경우 [사용자 모드 덤프 파일: 가장](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 적합 한 도구를 선택 하 여 덤프를 생성 하는 데 적합 한 도구 선택을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-474">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="48d29-475">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="48d29-475">Analyze the dump</span></span>

<span data-ttu-id="48d29-476">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-476">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="48d29-477">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-477">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="48d29-478">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="48d29-478">Clear package caches</span></span>

<span data-ttu-id="48d29-479">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-479">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="48d29-480">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-480">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="48d29-481">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-481">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="48d29-482">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-482">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="48d29-483">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-483">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="48d29-484">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-484">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="48d29-485">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-485">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="48d29-486">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-486">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="48d29-487">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-487">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48d29-488">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="48d29-488">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="48d29-489">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-489">Azure documentation</span></span>

* [<span data-ttu-id="48d29-490">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="48d29-490">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="48d29-491">Visual Studio를 사용 하 여 Azure App Service에서 웹 앱 문제 해결의 원격 디버깅 웹 앱 섹션</span><span class="sxs-lookup"><span data-stu-id="48d29-491">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="48d29-492">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="48d29-492">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="48d29-493">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="48d29-493">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="48d29-494">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-494">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="48d29-495">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-495">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="48d29-496">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-496">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="48d29-497">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="48d29-497">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="48d29-498">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="48d29-498">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="48d29-499">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 비디오)</span><span class="sxs-lookup"><span data-stu-id="48d29-499">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="48d29-500">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-500">Visual Studio documentation</span></span>

* [<span data-ttu-id="48d29-501">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48d29-501">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="48d29-502">Visual Studio 2017 원격 IIS 컴퓨터의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48d29-502">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="48d29-503">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="48d29-503">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="48d29-504">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-504">Visual Studio Code documentation</span></span>

* <span data-ttu-id="48d29-505">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="48d29-505">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="48d29-506">이 문서에서는 일반적인 앱 시작 오류에 대 한 정보와 앱이 Azure App Service 또는 IIS에 배포 될 때 오류를 진단 하는 방법에 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-506">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="48d29-507">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-507">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="48d29-508">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-508">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="48d29-509">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-509">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="48d29-510">Azure App Service에 배포 된 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-510">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="48d29-511">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-511">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="48d29-512">IIS에 배포 되었거나 로컬로 IIS Express에서 실행 되는 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-512">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="48d29-513">지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-513">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="48d29-514">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="48d29-514">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="48d29-515">Incoherent 패키지가 주요 업그레이드를 수행 하거나 패키지 버전을 변경할 때 앱이 중단 되는 경우 수행할 작업을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-515">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="48d29-516">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="48d29-516">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="48d29-517">추가 문제 해결 항목을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-517">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="48d29-518">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-518">App startup errors</span></span>

<span data-ttu-id="48d29-519">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-519">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="48d29-520">이 항목의 지침을 사용 하 여 로컬로 디버그할 수 있는 경우 발생 하는 *502.5-프로세스 오류* 또는 *500.30 시작 오류* 입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-520">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="48d29-521">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="48d29-521">403.14 Forbidden</span></span>

<span data-ttu-id="48d29-522">앱이 시작 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-522">The app fails to start.</span></span> <span data-ttu-id="48d29-523">다음 오류가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-523">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="48d29-524">오류는 일반적으로 다음과 같은 시나리오를 포함 하 여 호스팅 시스템의 끊어진 배포로 인해 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-524">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="48d29-525">응용 프로그램이 호스팅 시스템의 잘못 된 폴더에 배포 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-525">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="48d29-526">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-526">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="48d29-527">Web.config *파일이 배포* 에 없거나 *web.config 파일 내용의* 형식이 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-527">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="48d29-528">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-528">Perform the following steps:</span></span>

1. <span data-ttu-id="48d29-529">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-529">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="48d29-530">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용 하 여 앱의 *게시* 폴더 내용을 호스팅 시스템에 다시 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-530">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="48d29-531">*Web.config* 파일이 배포에 있고 해당 콘텐츠가 올바른지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-531">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="48d29-532">Azure App Service에서 호스팅할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-532">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="48d29-533">IIS에서 응용 프로그램을 호스트 하는 경우 iis **관리자**의 **기본 설정**에 표시 된 iis **실제 경로** 에 앱이 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-533">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="48d29-534">호스팅 시스템의 배포를 프로젝트의 *게시* 폴더의 콘텐츠와 비교 하 여 모든 앱의 파일 및 폴더가 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-534">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="48d29-535">게시 된 ASP.NET Core 앱의 레이아웃에 대 한 자세한 내용은 <xref:host-and-deploy/directory-structure>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-535">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="48d29-536">*Web.config 파일에* 대 한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-536">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="48d29-537">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-537">500 Internal Server Error</span></span>

<span data-ttu-id="48d29-538">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-538">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="48d29-539">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-539">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="48d29-540">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-540">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="48d29-541">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-541">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="48d29-542">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-542">From the server's perspective, that's correct.</span></span> <span data-ttu-id="48d29-543">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-543">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="48d29-544">서버의 명령 프롬프트에서 앱을 실행 하거나 ASP.NET Core 모듈 stdout 로그를 사용 하 여 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-544">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="48d29-545">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-545">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="48d29-546">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-546">The worker process fails.</span></span> <span data-ttu-id="48d29-547">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-547">The app doesn't start.</span></span>

<span data-ttu-id="48d29-548">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) .NET Core CLR을 찾지 못하며 in-process 요청 처리기 (*aspnetcorev2_inprocess*)를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-548">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="48d29-549">다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-549">Check that:</span></span>

* <span data-ttu-id="48d29-550">앱은 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 패키지 또는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 중 하나를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-550">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="48d29-551">앱이 대상으로 하는 ASP.NET Core 공유 프레임워크의 버전이 대상 머신에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-551">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="48d29-552">500.0 Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-552">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="48d29-553">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-553">The worker process fails.</span></span> <span data-ttu-id="48d29-554">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-554">The app doesn't start.</span></span>

<span data-ttu-id="48d29-555">[ASP.NET Core 모듈이](xref:host-and-deploy/aspnet-core-module) out-of-process 호스팅 요청 처리기를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-555">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="48d29-556">*aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 옆의 하위 폴더에 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-556">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="48d29-557">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-557">502.5 Process Failure</span></span>

<span data-ttu-id="48d29-558">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-558">The worker process fails.</span></span> <span data-ttu-id="48d29-559">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-559">The app doesn't start.</span></span>

<span data-ttu-id="48d29-560">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-560">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="48d29-561">프로세스 시작 오류의 원인은 일반적으로 응용 프로그램 이벤트 로그의 항목 및 ASP.NET Core 모듈 stdout 로그에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-561">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="48d29-562">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-562">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="48d29-563">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-563">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="48d29-564">*공유 프레임워크*는 머신에 설치되고 메타패키지(예: *)에서 참조되는 어셈블리(* .dll`Microsoft.AspNetCore.App` 파일) 세트입니다.{6}{7}</span><span class="sxs-lookup"><span data-stu-id="48d29-564">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="48d29-565">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-565">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="48d29-566">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-566">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="48d29-567">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-567">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="48d29-568">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="48d29-568">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="48d29-569">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-569">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="48d29-570">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-570">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="48d29-571">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-571">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="48d29-572">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-572">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="48d29-573">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-573">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="48d29-574">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-574">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="48d29-575">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-575">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="48d29-576">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-576">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="48d29-577">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시 된 비트 간에 충돌이 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-577">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="48d29-578">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="48d29-578">Connection reset</span></span>

<span data-ttu-id="48d29-579">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-579">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="48d29-580">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-580">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="48d29-581">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-581">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="48d29-582">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-582">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="48d29-583">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="48d29-583">Default startup limits</span></span>

<span data-ttu-id="48d29-584">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 은 기본 *startupTimeLimit* 120 초를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="48d29-585">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-585">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="48d29-586">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-586">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="48d29-587">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-587">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="48d29-588">응용 프로그램 이벤트 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-588">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="48d29-589">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-589">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="48d29-590">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-590">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="48d29-591">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-591">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="48d29-592">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-592">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="48d29-593">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-593">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="48d29-594">*원본* 열에서 *IIS AspNetCoreModule* 또는 **IIS AspNetCoreModule V2** 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-594">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="48d29-595">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-595">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="48d29-596">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-596">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-597">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-597">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-598">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-598">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-599">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-599">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-600">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-600">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="48d29-601">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-601">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="48d29-602">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-602">Examine the log.</span></span> <span data-ttu-id="48d29-603">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-603">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="48d29-604">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-604">Run the app in the Kudu console</span></span>

<span data-ttu-id="48d29-605">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-605">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="48d29-606">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-606">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="48d29-607">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-607">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-608">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-608">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-609">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-609">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-610">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-610">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="48d29-611">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="48d29-611">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="48d29-612">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="48d29-612">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="48d29-613">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-613">Run the app:</span></span>
   * <span data-ttu-id="48d29-614">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-614">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="48d29-615">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-615">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="48d29-616">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-616">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="48d29-617">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="48d29-617">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="48d29-618">*ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.* {3}{4}</span><span class="sxs-lookup"><span data-stu-id="48d29-618">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="48d29-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="48d29-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="48d29-620">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-620">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="48d29-621">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-621">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="48d29-622">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="48d29-622">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="48d29-623">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="48d29-623">**Current release**</span></span>

* <span data-ttu-id="48d29-624">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-624">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="48d29-625">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-625">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="48d29-626">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-626">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="48d29-627">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="48d29-627">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="48d29-628">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-628">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="48d29-629">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="48d29-629">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="48d29-630">*ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.* {3}{4}</span><span class="sxs-lookup"><span data-stu-id="48d29-630">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="48d29-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="48d29-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="48d29-632">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-632">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="48d29-633">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-633">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="48d29-634">ASP.NET Core 모듈 stdout 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-634">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="48d29-635">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-635">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="48d29-636">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-636">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-637">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-637">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="48d29-638">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-638">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="48d29-639">**권장 해결 방법** > **Stdout 로그 리디렉션 사용**에서 단추를 선택 하 여 **Kudu 콘솔을 열고 web.config를 편집**합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-639">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="48d29-640">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-640">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="48d29-641">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-641">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="48d29-642">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-642">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-643">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-643">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="48d29-644">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-644">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="48d29-645">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-645">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-646">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-646">Return to the Azure portal.</span></span> <span data-ttu-id="48d29-647">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-647">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="48d29-648">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-648">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-649">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-649">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-650">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-650">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-651">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-651">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="48d29-652">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-652">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="48d29-653">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-653">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="48d29-654">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-654">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="48d29-655">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-655">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="48d29-656">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-656">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="48d29-657">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-657">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="48d29-658">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-658">Select **Save** to save the file.</span></span>

<span data-ttu-id="48d29-659">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-659">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-660">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-660">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-661">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-661">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="48d29-662">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-662">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="48d29-663">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-663">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-664">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-664">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="48d29-665">ASP.NET Core 모듈 디버그 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-665">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="48d29-666">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-666">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="48d29-667">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-667">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-668">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-668">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="48d29-669">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-669">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="48d29-670">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-670">Redeploy the app.</span></span>
   * <span data-ttu-id="48d29-671">Kudu 콘솔을 사용하여 `<handlerSettings>`개선된 진단 로그[에 표시되는 ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-671">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="48d29-672">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-672">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-673">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-673">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-674">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-674">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="48d29-675">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-675">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="48d29-676">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-676">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="48d29-677">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-677">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="48d29-678">`<handlerSettings>`개선된 진단 로그[에 표시된 대로 ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-678">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="48d29-679">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-679">Select the **Save** button.</span></span>
1. <span data-ttu-id="48d29-680">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-681">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-682">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-682">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-683">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-684">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="48d29-685">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-685">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="48d29-686">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-686">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="48d29-687">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-687">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="48d29-688">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-688">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="48d29-689">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-689">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="48d29-690">`<handlerSettings>`web.config*파일에서*를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-690">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="48d29-691">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-691">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="48d29-692">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-692">Save the file.</span></span>

<span data-ttu-id="48d29-693">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-693">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-694">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-694">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-695">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-695">There's no limit on log file size.</span></span> <span data-ttu-id="48d29-696">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-696">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="48d29-697">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-697">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-698">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-698">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="48d29-699">느리거나 중단 앱 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-699">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="48d29-700">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-700">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="48d29-701">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-701">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="48d29-702">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="48d29-702">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="48d29-703">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="48d29-703">Monitoring blades</span></span>

<span data-ttu-id="48d29-704">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-704">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="48d29-705">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-705">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="48d29-706">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-706">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="48d29-707">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-707">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="48d29-708">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-708">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="48d29-709">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-709">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="48d29-710">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-710">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="48d29-711">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-711">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="48d29-712">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-712">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="48d29-713">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-713">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="48d29-714">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-714">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="48d29-715">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-715">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="48d29-716">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-716">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="48d29-717">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-717">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-718">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-718">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-719">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-719">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-720">**Wwwroot** 의 경로 **사이트** > 폴더를 열고 아래로 스크롤하여 목록의 맨 아래에 있는 *web.config* 파일을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-720">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="48d29-721">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-721">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-722">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-722">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="48d29-723">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-723">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="48d29-724">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-724">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="48d29-725">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-725">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="48d29-726">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-726">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="48d29-727">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-727">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="48d29-728">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-728">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="48d29-729">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-729">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="48d29-730">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-730">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-731">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-731">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="48d29-732">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-732">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="48d29-733">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-733">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="48d29-734">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-734">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="48d29-735">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-735">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="48d29-736">[Azure App Service에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ: 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-736">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="48d29-737">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-737">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-738">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-738">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-739">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-739">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="48d29-740">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-740">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-741">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-741">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="48d29-742">IIS에 대 한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-742">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="48d29-743">응용 프로그램 이벤트 로그 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-743">Application Event Log (IIS)</span></span>

<span data-ttu-id="48d29-744">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-744">Access the Application Event Log:</span></span>

1. <span data-ttu-id="48d29-745">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-745">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="48d29-746">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-746">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="48d29-747">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-747">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="48d29-748">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-748">Search for errors associated with the failing app.</span></span> <span data-ttu-id="48d29-749">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-749">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="48d29-750">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-750">Run the app at a command prompt</span></span>

<span data-ttu-id="48d29-751">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-751">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="48d29-752">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-752">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="48d29-753">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="48d29-753">Framework-dependent deployment</span></span>

<span data-ttu-id="48d29-754">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-754">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="48d29-755">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-755">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="48d29-756">\< 명령에서 `dotnet .\<assembly_name>.dll`assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-756">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="48d29-757">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-757">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="48d29-758">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-758">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="48d29-759">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-759">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="48d29-760">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-760">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="48d29-761">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="48d29-761">Self-contained deployment</span></span>

<span data-ttu-id="48d29-762">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-762">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="48d29-763">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-763">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="48d29-764">\< 명령에서 `<assembly_name>.exe`assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="48d29-765">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="48d29-766">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="48d29-767">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="48d29-768">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="48d29-769">ASP.NET Core 모듈 stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-769">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="48d29-770">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-770">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-771">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-771">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="48d29-772">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-772">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="48d29-773">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-773">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="48d29-774">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-774">Edit the *web.config* file.</span></span> <span data-ttu-id="48d29-775">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="48d29-775">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="48d29-776">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-776">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="48d29-777">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-777">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="48d29-778">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-778">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="48d29-779">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-779">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="48d29-780">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-780">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="48d29-781">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-781">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-782">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-782">Navigate to the *logs* folder.</span></span> <span data-ttu-id="48d29-783">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-783">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="48d29-784">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-784">Study the log for errors.</span></span>

<span data-ttu-id="48d29-785">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-785">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="48d29-786">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-786">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-787">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-787">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="48d29-788">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-788">Save the file.</span></span>

<span data-ttu-id="48d29-789">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-789">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-790">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-790">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-791">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-791">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="48d29-792">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-792">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-793">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-793">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="48d29-794">ASP.NET Core 모듈 디버그 로그 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-794">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="48d29-795">응용 프로그램의 *web.config* 파일에 다음 처리기 설정을 추가 하 여 ASP.NET Core 모듈 디버그 로그를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-795">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="48d29-796">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-796">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="48d29-797">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-797">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="48d29-798">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="48d29-798">Enable the Developer Exception Page</span></span>

<span data-ttu-id="48d29-799">`ASPNETCORE_ENVIRONMENT` [환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 하 여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-799">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="48d29-800">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-800">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="48d29-801">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-801">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="48d29-802">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-802">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="48d29-803">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-803">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="48d29-804">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="48d29-804">Obtain data from an app</span></span>

<span data-ttu-id="48d29-805">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-805">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="48d29-806">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-806">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="48d29-807">느리거나 중단 앱 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-807">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="48d29-808">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-808">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="48d29-809">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="48d29-809">App crashes or encounters an exception</span></span>

<span data-ttu-id="48d29-810">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-810">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="48d29-811">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-811">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="48d29-812">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-812">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="48d29-813">[EnableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-813">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="48d29-814">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-814">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="48d29-815">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-815">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="48d29-816">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-816">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="48d29-817">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-817">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="48d29-818">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-818">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="48d29-819">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-819">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="48d29-820">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-820">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="48d29-821">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-821">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-822">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-822">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="48d29-823">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-823">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="48d29-824">*앱이 중지 되* 는 경우 (응답을 중지 하지만 충돌 하지 않음), 시작 중에 실패 하거나 정상적으로 실행 되는 경우 [사용자 모드 덤프 파일: 가장](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 적합 한 도구를 선택 하 여 덤프를 생성 하는 데 적합 한 도구 선택을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-824">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="48d29-825">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="48d29-825">Analyze the dump</span></span>

<span data-ttu-id="48d29-826">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-826">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="48d29-827">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-827">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="48d29-828">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="48d29-828">Clear package caches</span></span>

<span data-ttu-id="48d29-829">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-829">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="48d29-830">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-830">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="48d29-831">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-831">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="48d29-832">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-832">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="48d29-833">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-833">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="48d29-834">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-834">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="48d29-835">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-835">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="48d29-836">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-836">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="48d29-837">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-837">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48d29-838">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="48d29-838">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="48d29-839">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-839">Azure documentation</span></span>

* [<span data-ttu-id="48d29-840">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="48d29-840">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="48d29-841">Visual Studio를 사용 하 여 Azure App Service에서 웹 앱 문제 해결의 원격 디버깅 웹 앱 섹션</span><span class="sxs-lookup"><span data-stu-id="48d29-841">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="48d29-842">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="48d29-842">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="48d29-843">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="48d29-843">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="48d29-844">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-844">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="48d29-845">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-845">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="48d29-846">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-846">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="48d29-847">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="48d29-847">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="48d29-848">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="48d29-848">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="48d29-849">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 비디오)</span><span class="sxs-lookup"><span data-stu-id="48d29-849">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="48d29-850">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-850">Visual Studio documentation</span></span>

* [<span data-ttu-id="48d29-851">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48d29-851">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="48d29-852">Visual Studio 2017 원격 IIS 컴퓨터의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48d29-852">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="48d29-853">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="48d29-853">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="48d29-854">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-854">Visual Studio Code documentation</span></span>

* <span data-ttu-id="48d29-855">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="48d29-855">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="48d29-856">이 문서에서는 일반적인 앱 시작 오류에 대 한 정보와 앱이 Azure App Service 또는 IIS에 배포 될 때 오류를 진단 하는 방법에 대 한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-856">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="48d29-857">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-857">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="48d29-858">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-858">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="48d29-859">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-859">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="48d29-860">Azure App Service에 배포 된 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-860">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="48d29-861">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-861">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="48d29-862">IIS에 배포 되었거나 로컬로 IIS Express에서 실행 되는 앱에 대 한 문제 해결 조언을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-862">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="48d29-863">지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-863">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="48d29-864">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="48d29-864">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="48d29-865">Incoherent 패키지가 주요 업그레이드를 수행 하거나 패키지 버전을 변경할 때 앱이 중단 되는 경우 수행할 작업을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-865">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="48d29-866">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="48d29-866">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="48d29-867">추가 문제 해결 항목을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-867">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="48d29-868">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-868">App startup errors</span></span>

<span data-ttu-id="48d29-869">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-869">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="48d29-870">로컬로 디버그할 때 발생 하는 *502.5 프로세스 오류* 는이 항목의 지침을 사용 하 여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-870">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="48d29-871">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="48d29-871">403.14 Forbidden</span></span>

<span data-ttu-id="48d29-872">앱이 시작 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-872">The app fails to start.</span></span> <span data-ttu-id="48d29-873">다음 오류가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-873">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="48d29-874">오류는 일반적으로 다음과 같은 시나리오를 포함 하 여 호스팅 시스템의 끊어진 배포로 인해 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-874">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="48d29-875">응용 프로그램이 호스팅 시스템의 잘못 된 폴더에 배포 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-875">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="48d29-876">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동 하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-876">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="48d29-877">Web.config *파일이 배포* 에 없거나 *web.config 파일 내용의* 형식이 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-877">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="48d29-878">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-878">Perform the following steps:</span></span>

1. <span data-ttu-id="48d29-879">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-879">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="48d29-880">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용 하 여 앱의 *게시* 폴더 내용을 호스팅 시스템에 다시 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-880">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="48d29-881">*Web.config* 파일이 배포에 있고 해당 콘텐츠가 올바른지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-881">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="48d29-882">Azure App Service에서 호스팅할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-882">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="48d29-883">IIS에서 응용 프로그램을 호스트 하는 경우 iis **관리자**의 **기본 설정**에 표시 된 iis **실제 경로** 에 앱이 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-883">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="48d29-884">호스팅 시스템의 배포를 프로젝트의 *게시* 폴더의 콘텐츠와 비교 하 여 모든 앱의 파일 및 폴더가 배포 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-884">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="48d29-885">게시 된 ASP.NET Core 앱의 레이아웃에 대 한 자세한 내용은 <xref:host-and-deploy/directory-structure>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-885">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="48d29-886">*Web.config 파일에* 대 한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-886">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="48d29-887">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="48d29-887">500 Internal Server Error</span></span>

<span data-ttu-id="48d29-888">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-888">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="48d29-889">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-889">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="48d29-890">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-890">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="48d29-891">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-891">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="48d29-892">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-892">From the server's perspective, that's correct.</span></span> <span data-ttu-id="48d29-893">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-893">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="48d29-894">서버의 명령 프롬프트에서 앱을 실행 하거나 ASP.NET Core 모듈 stdout 로그를 사용 하 여 문제를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-894">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="48d29-895">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="48d29-895">502.5 Process Failure</span></span>

<span data-ttu-id="48d29-896">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-896">The worker process fails.</span></span> <span data-ttu-id="48d29-897">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-897">The app doesn't start.</span></span>

<span data-ttu-id="48d29-898">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-898">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="48d29-899">프로세스 시작 오류의 원인은 일반적으로 응용 프로그램 이벤트 로그의 항목 및 ASP.NET Core 모듈 stdout 로그에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-899">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="48d29-900">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-900">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="48d29-901">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-901">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="48d29-902">*공유 프레임워크*는 머신에 설치되고 메타패키지(예: *)에서 참조되는 어셈블리(* .dll`Microsoft.AspNetCore.App` 파일) 세트입니다.{6}{7}</span><span class="sxs-lookup"><span data-stu-id="48d29-902">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="48d29-903">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-903">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="48d29-904">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-904">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="48d29-905">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-905">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="48d29-906">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="48d29-906">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="48d29-907">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-907">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="48d29-908">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-908">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="48d29-909">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-909">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="48d29-910">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-910">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="48d29-911">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-911">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="48d29-912">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-912">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="48d29-913">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-913">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="48d29-914">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-914">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="48d29-915">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시 된 비트 간에 충돌이 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-915">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="48d29-916">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="48d29-916">Connection reset</span></span>

<span data-ttu-id="48d29-917">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-917">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="48d29-918">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-918">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="48d29-919">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-919">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="48d29-920">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-920">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="48d29-921">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="48d29-921">Default startup limits</span></span>

<span data-ttu-id="48d29-922">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 은 기본 *startupTimeLimit* 120 초를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-922">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="48d29-923">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-923">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="48d29-924">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-924">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="48d29-925">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-925">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="48d29-926">응용 프로그램 이벤트 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-926">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="48d29-927">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-927">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="48d29-928">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-928">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="48d29-929">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-929">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="48d29-930">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-930">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="48d29-931">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-931">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="48d29-932">*원본* 열에서 *IIS AspNetCoreModule* 또는 **IIS AspNetCoreModule V2** 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-932">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="48d29-933">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-933">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="48d29-934">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-934">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-935">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-935">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-936">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-936">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-937">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-937">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-938">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-938">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="48d29-939">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-939">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="48d29-940">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-940">Examine the log.</span></span> <span data-ttu-id="48d29-941">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-941">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="48d29-942">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-942">Run the app in the Kudu console</span></span>

<span data-ttu-id="48d29-943">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-943">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="48d29-944">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-944">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="48d29-945">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-945">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="48d29-946">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-946">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-947">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-947">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-948">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-948">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="48d29-949">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="48d29-949">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="48d29-950">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="48d29-950">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="48d29-951">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-951">Run the app:</span></span>
   * <span data-ttu-id="48d29-952">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-952">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="48d29-953">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-953">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="48d29-954">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-954">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="48d29-955">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="48d29-955">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="48d29-956">*ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.* {3}{4}</span><span class="sxs-lookup"><span data-stu-id="48d29-956">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="48d29-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="48d29-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="48d29-958">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-958">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="48d29-959">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-959">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="48d29-960">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="48d29-960">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="48d29-961">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="48d29-961">**Current release**</span></span>

* <span data-ttu-id="48d29-962">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-962">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="48d29-963">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-963">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="48d29-964">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-964">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="48d29-965">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="48d29-965">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="48d29-966">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-966">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="48d29-967">**미리 보기 릴리스에서 실행 되는 프레임 워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="48d29-967">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="48d29-968">*ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.* {3}{4}</span><span class="sxs-lookup"><span data-stu-id="48d29-968">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="48d29-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="48d29-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="48d29-970">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="48d29-970">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="48d29-971">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-971">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="48d29-972">ASP.NET Core 모듈 stdout 로그 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-972">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="48d29-973">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-973">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="48d29-974">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-974">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-975">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-975">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="48d29-976">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-976">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="48d29-977">**권장 해결 방법** > **Stdout 로그 리디렉션 사용**에서 단추를 선택 하 여 **Kudu 콘솔을 열고 web.config를 편집**합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-977">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="48d29-978">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-978">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="48d29-979">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-979">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="48d29-980">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-980">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-981">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-981">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="48d29-982">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-982">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="48d29-983">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-983">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-984">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-984">Return to the Azure portal.</span></span> <span data-ttu-id="48d29-985">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-985">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="48d29-986">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-986">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-987">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-987">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-988">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-988">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-989">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-989">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="48d29-990">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-990">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="48d29-991">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-991">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="48d29-992">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-992">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="48d29-993">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-993">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="48d29-994">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-994">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="48d29-995">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-995">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="48d29-996">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-996">Select **Save** to save the file.</span></span>

<span data-ttu-id="48d29-997">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-997">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-998">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-998">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-999">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-999">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="48d29-1000">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1000">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="48d29-1001">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1001">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-1002">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1002">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="48d29-1003">느리거나 중단 앱 (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="48d29-1003">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="48d29-1004">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1004">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="48d29-1005">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-1005">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="48d29-1006">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="48d29-1006">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="48d29-1007">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="48d29-1007">Monitoring blades</span></span>

<span data-ttu-id="48d29-1008">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1008">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="48d29-1009">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1009">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="48d29-1010">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1010">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="48d29-1011">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1011">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="48d29-1012">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1012">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="48d29-1013">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1013">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="48d29-1014">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1014">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="48d29-1015">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1015">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="48d29-1016">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1016">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="48d29-1017">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1017">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="48d29-1018">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1018">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="48d29-1019">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1019">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="48d29-1020">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1020">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="48d29-1021">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1021">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="48d29-1022">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1022">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="48d29-1023">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1023">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="48d29-1024">**Wwwroot** 의 경로 **사이트** > 폴더를 열고 아래로 스크롤하여 목록의 맨 아래에 있는 *web.config* 파일을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1024">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="48d29-1025">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1025">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-1026">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1026">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="48d29-1027">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1027">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="48d29-1028">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1028">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="48d29-1029">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1029">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="48d29-1030">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1030">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="48d29-1031">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1031">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="48d29-1032">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1032">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="48d29-1033">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1033">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="48d29-1034">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1034">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-1035">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1035">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="48d29-1036">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1036">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="48d29-1037">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-1037">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="48d29-1038">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1038">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="48d29-1039">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1039">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="48d29-1040">[Azure App Service에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ: 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1040">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="48d29-1041">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1041">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-1042">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1042">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-1043">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1043">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="48d29-1044">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1044">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-1045">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1045">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="48d29-1046">IIS에 대 한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-1046">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="48d29-1047">응용 프로그램 이벤트 로그 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-1047">Application Event Log (IIS)</span></span>

<span data-ttu-id="48d29-1048">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1048">Access the Application Event Log:</span></span>

1. <span data-ttu-id="48d29-1049">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1049">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="48d29-1050">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1050">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="48d29-1051">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1051">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="48d29-1052">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1052">Search for errors associated with the failing app.</span></span> <span data-ttu-id="48d29-1053">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1053">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="48d29-1054">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-1054">Run the app at a command prompt</span></span>

<span data-ttu-id="48d29-1055">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1055">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="48d29-1056">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1056">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="48d29-1057">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="48d29-1057">Framework-dependent deployment</span></span>

<span data-ttu-id="48d29-1058">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-1058">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="48d29-1059">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1059">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="48d29-1060">\< 명령에서 `dotnet .\<assembly_name>.dll`assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1060">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="48d29-1061">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1061">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="48d29-1062">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1062">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="48d29-1063">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1063">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="48d29-1064">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1064">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="48d29-1065">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="48d29-1065">Self-contained deployment</span></span>

<span data-ttu-id="48d29-1066">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="48d29-1066">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="48d29-1067">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1067">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="48d29-1068">\< 명령에서 `<assembly_name>.exe`assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="48d29-1069">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="48d29-1070">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="48d29-1071">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="48d29-1072">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="48d29-1073">ASP.NET Core 모듈 stdout log (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-1073">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="48d29-1074">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="48d29-1074">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="48d29-1075">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1075">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="48d29-1076">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1076">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="48d29-1077">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1077">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="48d29-1078">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1078">Edit the *web.config* file.</span></span> <span data-ttu-id="48d29-1079">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="48d29-1079">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="48d29-1080">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1080">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="48d29-1081">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1081">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="48d29-1082">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1082">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="48d29-1083">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1083">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="48d29-1084">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1084">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="48d29-1085">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1085">Make a request to the app.</span></span>
1. <span data-ttu-id="48d29-1086">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1086">Navigate to the *logs* folder.</span></span> <span data-ttu-id="48d29-1087">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1087">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="48d29-1088">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1088">Study the log for errors.</span></span>

<span data-ttu-id="48d29-1089">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1089">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="48d29-1090">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1090">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="48d29-1091">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1091">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="48d29-1092">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1092">Save the file.</span></span>

<span data-ttu-id="48d29-1093">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1093">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-1094">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1094">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="48d29-1095">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1095">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="48d29-1096">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1096">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="48d29-1097">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1097">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="48d29-1098">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="48d29-1098">Enable the Developer Exception Page</span></span>

<span data-ttu-id="48d29-1099">`ASPNETCORE_ENVIRONMENT` [환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 하 여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1099">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="48d29-1100">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1100">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="48d29-1101">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1101">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="48d29-1102">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1102">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="48d29-1103">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1103">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="48d29-1104">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="48d29-1104">Obtain data from an app</span></span>

<span data-ttu-id="48d29-1105">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1105">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="48d29-1106">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1106">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="48d29-1107">느리거나 중단 앱 (IIS)</span><span class="sxs-lookup"><span data-stu-id="48d29-1107">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="48d29-1108">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1108">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="48d29-1109">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="48d29-1109">App crashes or encounters an exception</span></span>

<span data-ttu-id="48d29-1110">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1110">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="48d29-1111">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1111">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="48d29-1112">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1112">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="48d29-1113">[EnableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-1113">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="48d29-1114">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-1114">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="48d29-1115">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-1115">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="48d29-1116">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1116">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="48d29-1117">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-1117">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="48d29-1118">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-1118">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="48d29-1119">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="48d29-1119">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="48d29-1120">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1120">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="48d29-1121">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1121">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="48d29-1122">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1122">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="48d29-1123">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="48d29-1123">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="48d29-1124">*앱이 중지 되* 는 경우 (응답을 중지 하지만 충돌 하지 않음), 시작 중에 실패 하거나 정상적으로 실행 되는 경우 [사용자 모드 덤프 파일: 가장](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) 적합 한 도구를 선택 하 여 덤프를 생성 하는 데 적합 한 도구 선택을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1124">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="48d29-1125">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="48d29-1125">Analyze the dump</span></span>

<span data-ttu-id="48d29-1126">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1126">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="48d29-1127">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48d29-1127">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="48d29-1128">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="48d29-1128">Clear package caches</span></span>

<span data-ttu-id="48d29-1129">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1129">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="48d29-1130">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1130">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="48d29-1131">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1131">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="48d29-1132">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1132">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="48d29-1133">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1133">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="48d29-1134">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1134">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="48d29-1135">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1135">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="48d29-1136">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1136">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="48d29-1137">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="48d29-1137">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48d29-1138">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="48d29-1138">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="48d29-1139">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-1139">Azure documentation</span></span>

* [<span data-ttu-id="48d29-1140">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="48d29-1140">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="48d29-1141">Visual Studio를 사용 하 여 Azure App Service에서 웹 앱 문제 해결의 원격 디버깅 웹 앱 섹션</span><span class="sxs-lookup"><span data-stu-id="48d29-1141">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="48d29-1142">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="48d29-1142">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="48d29-1143">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="48d29-1143">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="48d29-1144">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-1144">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="48d29-1145">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-1145">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="48d29-1146">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="48d29-1146">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="48d29-1147">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="48d29-1147">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="48d29-1148">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="48d29-1148">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="48d29-1149">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 비디오)</span><span class="sxs-lookup"><span data-stu-id="48d29-1149">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="48d29-1150">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-1150">Visual Studio documentation</span></span>

* [<span data-ttu-id="48d29-1151">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48d29-1151">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="48d29-1152">Visual Studio 2017 원격 IIS 컴퓨터의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48d29-1152">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="48d29-1153">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="48d29-1153">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="48d29-1154">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="48d29-1154">Visual Studio Code documentation</span></span>

* <span data-ttu-id="48d29-1155">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="48d29-1155">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end
