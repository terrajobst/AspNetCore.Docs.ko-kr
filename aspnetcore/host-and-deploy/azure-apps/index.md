---
title: Azure App Service에 ASP.NET Core 앱 배포
author: bradygaster
description: 이 문서에는 Azure 호스트 및 배포 리소스의 링크가 포함됩니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644613"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="99bd1-103">Azure App Service에 ASP.NET Core 앱 배포</span><span class="sxs-lookup"><span data-stu-id="99bd1-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="99bd1-104">[Azure App Service](https://azure.microsoft.com/services/app-service/)는 ASP.NET Core를 비롯한 웹앱을 호스트하기 위한 [Microsoft 클라우드 컴퓨팅 플랫폼 서비스](https://azure.microsoft.com/)입니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="99bd1-105">유용한 리소스</span><span class="sxs-lookup"><span data-stu-id="99bd1-105">Useful resources</span></span>

<span data-ttu-id="99bd1-106">[App Service 설명서](/azure/app-service/)에서는 Azure 앱 설명서, 자습서, 샘플, 방법 가이드 및 기타 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="99bd1-107">ASP.NET Core 앱 호스트와 관련하여 두 가지 주목할 만한 자습서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="99bd1-108">Azure에서 ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="99bd1-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="99bd1-109">Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 Windows의 Azure App Service에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="99bd1-110">App Service on Linux에서 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="99bd1-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="99bd1-111">명령줄을 사용하여 ASP.NET Core 웹앱을 만들고 Linux의 Azure App Service에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="99bd1-112">Azure 앱 서비스에서 사용할 수 있는 ASP.NET Core 버전은 [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="99bd1-113">[App Service 공지](https://github.com/Azure/app-service-announcements/) 리포지토리를 구독하고 문제를 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="99bd1-114">App Service 팀은 App Service에 도착하는 공지 및 시나리오를 정기적으로 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="99bd1-115">다음 문서는 ASP.NET Core 설명서에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="99bd1-116">Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 앱을 게시하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="99bd1-117">Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 위한 Git을 사용하여 Azure App Service에 배포하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="99bd1-118">첫 번째 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="99bd1-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="99bd1-119">ASP.NET Core 앱에 대한 CI 빌드를 설정하고 Azure App Service에 대한 연속 배포 릴리스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="99bd1-120">Azure Web App 샌드박스</span><span class="sxs-lookup"><span data-stu-id="99bd1-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="99bd1-121">Azure 앱 플랫폼에서 적용하는 Azure App Service 런타임 실행 제한 사항을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="99bd1-122">ASP.NET Core 프로젝트를 사용하여 경고 및 오류를 이해하고 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="99bd1-123">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="99bd1-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="99bd1-124">플랫폼</span><span class="sxs-lookup"><span data-stu-id="99bd1-124">Platform</span></span>

<span data-ttu-id="99bd1-125">A 시리즈 컴퓨팅(기본) 또는 그 이상의 호스트 계층에서 호스트되는 앱의 경우 Azure Portal의 앱 설정에서 App Services 앱의 플랫폼 아키텍처(x86/x64)가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="99bd1-126">앱의 게시 설정(예: Visual Studio [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles))이 Azure Portal의 앱 서비스 구성 설정과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="99bd1-127">64비트(x64) 및 32비트(x86) 앱은 Azure App Service에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="99bd1-128">App Service에서 사용 가능한 [.NET Core SDK](/dotnet/core/sdk)는 32비트이지만 [Kudu](https://github.com/projectkudu/kudu/wiki) 콘솔 또는 Visual Studio의 게시 프로세스를 사용하여 로컬로 빌드된 64비트 앱을 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="99bd1-129">자세한 내용은 [앱 게시 및 배포](#publish-and-deploy-the-app) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="99bd1-130">원시 종속 항목을 지원하는 앱의 경우 32비트(x86) 앱의 런타임이 Azure App Service에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="99bd1-131">App Service에서 사용 가능한 [.NET Core SDK](/dotnet/core/sdk)는 32비트입니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="99bd1-132">.NET Core 런타임 및 .NET Core SDK에 대한 정보와 같은 .NET Core 프레임워크 구성 요소 및 배포 방법에 대한 자세한 내용은 [.NET Core: 컴퍼지션 정보](/dotnet/core/about#composition)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="99bd1-133">패키지</span><span class="sxs-lookup"><span data-stu-id="99bd1-133">Packages</span></span>

<span data-ttu-id="99bd1-134">Azure App Service에 배포된 앱에 대한 자동 로깅 기능을 제공하려면 다음 NuGet 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="99bd1-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/)은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)을 사용하여 Azure App Service와 ASP.NET Core의 강화 통합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="99bd1-136">추가된 로깅 기능은 `Microsoft.AspNetCore.AzureAppServicesIntegration` 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="99bd1-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/)은 [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics)를 실행하여 `Microsoft.Extensions.Logging.AzureAppServices` 패키지의 Azure App Service 진단 로깅 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="99bd1-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)는 Azure App Service 진단 로그 및 로그 스트리밍 기능을 지원하는 로거 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="99bd1-139">이전의 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="99bd1-140">.NET Framework를 대상으로 하거나 `Microsoft.AspNetCore.App` 메타패키지를 참조하는 앱은 앱 프로젝트 파일의 개별 패키지를 명시적으로 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="99bd1-141">Azure Portal을 사용하여 앱 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="99bd1-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="99bd1-142">Azure Portal의 앱 설정을 사용하면 앱의 환경 변수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="99bd1-143">환경 변수는 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)가 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="99bd1-144">Azure Portal에서 앱 설정을 만들거나 수정하고**저장** 단추를 선택하면 Azure 앱이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="99bd1-145">서비스를 다시 시작한 후에 환경 변수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99bd1-146">앱이 [일반 호스트](xref:fundamentals/host/generic-host)를 사용하는 경우 호스트를 빌드하기 위해 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>가 호출될 때 환경 변수가 앱의 구성에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="99bd1-147">자세한 내용은 <xref:fundamentals/host/generic-host> 및 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="99bd1-148">앱이 [웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 호스트를 빌드하기 위해 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 호출될 때 환경 변수가 앱의 구성에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-148">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="99bd1-149">자세한 내용은 <xref:fundamentals/host/web-host> 및 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-149">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="99bd1-150">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="99bd1-150">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="99bd1-151">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-151">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="99bd1-152">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-152">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="99bd1-153">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-153">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="99bd1-154">모니터링 및 로깅</span><span class="sxs-lookup"><span data-stu-id="99bd1-154">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99bd1-155">App Service에 배포된 ASP.NET Core 앱은 자동으로 App Service 확장인 **ASP.NET Core 로깅 통합**을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-155">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="99bd1-156">확장을 사용하면 Azure App Service에서 ASP.NET Core 앱에서 로깅 통합이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-156">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="99bd1-157">App Service에 배포된 ASP.NET Core 앱은 자동으로 App Service 확장인 **ASP.NET Core 로깅 확장**을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-157">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="99bd1-158">확장을 사용하면 Azure App Service에서 ASP.NET Core 앱에서 로깅 통합이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-158">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="99bd1-159">모니터링, 로깅 및 문제 해결에 대한 자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-159">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="99bd1-160">Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="99bd1-160">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="99bd1-161">앱 및 App Service 계획의 할당량 및 메트릭을 검토하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-161">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="99bd1-162">Azure App Service에서 앱에 대한 진단 로깅 사용</span><span class="sxs-lookup"><span data-stu-id="99bd1-162">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="99bd1-163">HTTP 상태 코드, 실패한 요청 및 웹 서버 활동에 대한 진단 로깅을 활성화하고 액세스하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-163">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="99bd1-164">ASP.NET Core 앱에서 오류를 처리하기 위한 일반적인 접근법을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-164">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="99bd1-165">ASP.NET Core 앱을 사용하는 Azure App Service 배포에 대한 문제 진단 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-165">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="99bd1-166">Azure App Service/IIS에서 호스트하는 앱의 일반적인 배포 구성 오류와 문제 해결에 대한 조언을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-166">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="99bd1-167">데이터 보호 키 링 및 배포 슬롯</span><span class="sxs-lookup"><span data-stu-id="99bd1-167">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="99bd1-168">[데이터 보호 키](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)는 *%HOME%\ASP.NET\DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-168">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="99bd1-169">이 폴더는 네트워크 스토리지에서 지원하고, 앱을 호스트하는 모든 머신에서 동기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-169">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="99bd1-170">저장된 키는 보호되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-170">Keys aren't protected at rest.</span></span> <span data-ttu-id="99bd1-171">이 폴더는 단일 배포 슬롯에 앱의 모든 인스턴스에 대한 키 링을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-171">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="99bd1-172">준비 및 프로덕션과 같은 별도의 배포 슬롯은 키 링을 공유하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-172">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="99bd1-173">배포 슬롯을 바꿀 경우 데이터 보호를 사용하는 모든 시스템은 이전 슬롯 내에 있는 키 링을 사용하여 저장된 데이터의 암호를 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-173">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="99bd1-174">ASP.NET 쿠키 미들웨어는 데이터 보호를 사용하여 쿠키를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-174">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="99bd1-175">이로 인해 표준 ASP.NET 쿠키 미들웨어를 사용하는 앱에서 사용자가 로그아웃됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-175">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="99bd1-176">슬롯에 관계없는 키 링 솔루션의 경우 다음과 같은 외부 키 링 공급자를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-176">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="99bd1-177">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="99bd1-177">Azure Blob Storage</span></span>
* <span data-ttu-id="99bd1-178">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="99bd1-178">Azure Key Vault</span></span>
* <span data-ttu-id="99bd1-179">SQL 저장소</span><span class="sxs-lookup"><span data-stu-id="99bd1-179">SQL store</span></span>
* <span data-ttu-id="99bd1-180">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="99bd1-180">Redis cache</span></span>

<span data-ttu-id="99bd1-181">자세한 내용은 <xref:security/data-protection/implementation/key-storage-providers>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-181">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="99bd1-182">.NET Core 미리 보기를 사용하는 ASP.NET Core 앱 배포</span><span class="sxs-lookup"><span data-stu-id="99bd1-182">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="99bd1-183">.NET Core의 미리 보기 릴리스를 사용하는 앱을 배포하려면 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-183">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="99bd1-184">이러한 접근 방식은 런타임을 사용할 수 있지만, SDK가 Azure App Service에 설치되지 않은 경우에도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="99bd1-185">Azure Pipelines를 사용하여 .NET Core SDK 버전 지정</span><span class="sxs-lookup"><span data-stu-id="99bd1-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="99bd1-186">자체 포함 미리 보기 앱 배포</span><span class="sxs-lookup"><span data-stu-id="99bd1-186">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="99bd1-187">Web Apps for Containers에서 Docker 사용</span><span class="sxs-lookup"><span data-stu-id="99bd1-187">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="99bd1-188">미리 보기 사이트 확장 설치</span><span class="sxs-lookup"><span data-stu-id="99bd1-188">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="99bd1-189">Azure 앱 서비스에서 사용할 수 있는 ASP.NET Core 버전은 [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-189">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="99bd1-190">Azure Pipelines를 사용하여 .NET Core SDK 버전 지정</span><span class="sxs-lookup"><span data-stu-id="99bd1-190">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="99bd1-191">[Azure App Service CI/CD 시나리오](/azure/app-service/deploy-continuous-deployment)를 사용하여 Azure DevOps와 함께 연속 통합 빌드를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-191">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="99bd1-192">Azure DevOps 빌드를 만든 후에 원하는 경우 특정 SDK 버전을 사용하도록 빌드를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-192">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="99bd1-193">.NET Core SDK 버전 지정</span><span class="sxs-lookup"><span data-stu-id="99bd1-193">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="99bd1-194">App Service 배포 센터를 사용하여 Azure DevOps 빌드를 만들 때 기본 빌드 파이프라인에 `Restore`, `Build`, `Test` 및 `Publish`에 대한 단계가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-194">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="99bd1-195">SDK 버전을 지정하려면 에이전트 작업 목록에서 **추가(+)** 단추를 선택하여 새 단계를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-195">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="99bd1-196">검색 창에서 **.NET Core SDK**를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-196">Search for **.NET Core SDK** in the search bar.</span></span> 

![.NET Core SDK 단계 추가](index/add-sdk-step.png)

<span data-ttu-id="99bd1-198">다음 단계에서 지정된 .NET Core SDK 버전이 사용되도록 이 단계를 빌드의 첫 번째 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-198">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="99bd1-199">.NET Core SDK의 버전을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-199">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="99bd1-200">이 예제에서 SDK는 `3.0.100`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-200">In this example, the SDK is set to `3.0.100`.</span></span>

![완료된 SDK 단계](index/sdk-step-first-place.png)

<span data-ttu-id="99bd1-202">[SCD(자체 포함 게시)](/dotnet/core/deploying/#self-contained-deployments-scd)를 게시하려면 `Publish` 단계에서 SCD를 구성하고 [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-202">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![자체 포함 게시](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="99bd1-204">자체 포함 미리 보기 앱 배포</span><span class="sxs-lookup"><span data-stu-id="99bd1-204">Deploy a self-contained preview app</span></span>

<span data-ttu-id="99bd1-205">미리 보기 런타임을 대상으로 하는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)는 배포 시 미리 보기 런타임을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="99bd1-206">자체 포함된 앱을 배포하는 경우:</span><span class="sxs-lookup"><span data-stu-id="99bd1-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="99bd1-207">Azure App Service의 사이트에는 [미리 보기 사이트 확장](#install-the-preview-site-extension)이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="99bd1-208">앱은 [FDD(프레임워크 종속 배포)](/dotnet/core/deploying#framework-dependent-deployments-fdd)에 대해 게시할 때와 다른 접근 방식으로 공개해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="99bd1-209">[자체 포함된 앱 배포](#deploy-the-app-self-contained) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-209">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="99bd1-210">Web Apps for Containers에서 Docker 사용</span><span class="sxs-lookup"><span data-stu-id="99bd1-210">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="99bd1-211">[Docker 허브](https://hub.docker.com/r/microsoft/aspnetcore/)에는 최신 미리 보기 Docker 이미지가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-211">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="99bd1-212">이미지는 기본 이미지로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-212">The images can be used as a base image.</span></span> <span data-ttu-id="99bd1-213">이미지를 사용하고 일반적으로 Web App for Containers에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-213">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="99bd1-214">미리 보기 사이트 확장 설치</span><span class="sxs-lookup"><span data-stu-id="99bd1-214">Install the preview site extension</span></span>

<span data-ttu-id="99bd1-215">미리 보기 사이트 확장을 사용하는 데 문제가 발생하는 경우 [dotnet/AspNetCore 문제](https://github.com/dotnet/AspNetCore/issues)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-215">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="99bd1-216">Azure Portal에서 App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-216">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="99bd1-217">웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-217">Select the web app.</span></span>
1. <span data-ttu-id="99bd1-218">검색 상자에 "ex"를 입력하여 "확장"으로 필터링하거나 관리 도구 목록을 아래로 스크롤합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-218">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="99bd1-219">**확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-219">Select **Extensions**.</span></span>
1. <span data-ttu-id="99bd1-220">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-220">Select **Add**.</span></span>
1. <span data-ttu-id="99bd1-221">목록에서 **ASP.NET Core {X.Y}({x64|x86}) 런타임** 확장을 선택합니다. 여기서 `{X.Y}`는 ASP.NET Core 미리 보기 버전이며 `{x64|x86}`은 플랫폼을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-221">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="99bd1-222">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-222">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="99bd1-223">확장을 설치하려면 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-223">Select **OK** to install the extension.</span></span>

<span data-ttu-id="99bd1-224">작업이 완료되면 최신.NET Core 미리 보기가 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-224">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="99bd1-225">설치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-225">Verify the installation:</span></span>

1. <span data-ttu-id="99bd1-226">**고급 도구**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-226">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="99bd1-227">**고급 도구**에서 **Go**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-227">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="99bd1-228">**디버그 콘솔** > **PowerShell** 메뉴 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-228">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="99bd1-229">PowerShell 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-229">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="99bd1-230">명령에서 `{X.Y}`를 ASP.NET Core 런타임 버전으로 대체하고, `{PLATFORM}`을 플랫폼으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-230">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="99bd1-231">x64 미리 보기 런타임이 설치된 경우 명령이 `True`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-231">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="99bd1-232">A 시리즈 컴퓨팅(기본) 또는 그 이상의 호스트 계층에서 호스트되는 앱의 경우 Azure Portal의 앱 설정에서 App Services 앱의 플랫폼 아키텍처(x86/x64)가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-232">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="99bd1-233">앱의 게시 설정(예: Visual Studio [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles))이 Azure Portal의 앱 서비스 구성 설정과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-233">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="99bd1-234">앱이 in-process 모드에서 실행되고 플랫폼 아키텍처가 64비트(x64)에 대해 구성되는 경우 ASP.NET Core 모듈은 64비트 미리 보기 런타임을 사용합니다(있는 경우).</span><span class="sxs-lookup"><span data-stu-id="99bd1-234">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="99bd1-235">Azure Portal을 사용하여 **ASP.NET Core {X.Y}(x64) 런타임** 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-235">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="99bd1-236">x64 미리 보기 런타임을 설치한 후에 Azure Kudu PowerShell 명령 창에서 다음 명령을 실행하여 설치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-236">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="99bd1-237">다음 명령에서 `{X.Y}`에 대한 ASP.NET Core 런타임 버전을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-237">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="99bd1-238">x64 미리 보기 런타임이 설치된 경우 명령이 `True`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-238">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="99bd1-239">**ASP.NET Core 확장**을 사용하면 Azure 로깅을 사용하는 등 Azure App Services에서 ASP.NET Core에 대한 추가 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-239">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="99bd1-240">Visual Studio에서 배포할 경우 확장은 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-240">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="99bd1-241">확장이 설치되지 않은 경우 앱에서 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-241">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="99bd1-242">**ARM 템플릿에서 미리 보기 사이트 확장 사용**</span><span class="sxs-lookup"><span data-stu-id="99bd1-242">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="99bd1-243">ARM 템플릿을 사용하여 앱을 만들고 배포하는 경우 `siteextensions` 리소스 형식을 사용하여 웹앱에 사이트 확장을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-243">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="99bd1-244">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="99bd1-244">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="99bd1-245">앱 게시 및 배포</span><span class="sxs-lookup"><span data-stu-id="99bd1-245">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="99bd1-246">64 비트 배포의 경우:</span><span class="sxs-lookup"><span data-stu-id="99bd1-246">For a 64-bit deployment:</span></span>

* <span data-ttu-id="99bd1-247">64비트 .NET Core SDK를 사용하여 64비트 앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-247">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="99bd1-248">App Service의 **구성** > **일반 설정**에서 **플랫폼**을 **64비트**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-248">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="99bd1-249">플랫폼 비트 수를 선택할 수 있도록 하려면 앱에서 기본 이상의 서비스 플랜을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-249">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="99bd1-250">프레임워크 종속 앱 배포</span><span class="sxs-lookup"><span data-stu-id="99bd1-250">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99bd1-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99bd1-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="99bd1-252">Visual Studio 도구 모음에서 **빌드** > **{애플리케이션 이름} 게시**를 선택하거나 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="99bd1-253">**공개 대상 선택** 대화 상자에서 **App Service**가 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="99bd1-254">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-254">Select **Advanced**.</span></span> <span data-ttu-id="99bd1-255">**게시** 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="99bd1-256">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="99bd1-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="99bd1-257">**릴리스** 구성이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="99bd1-258">**배포 모드** 드롭다운 목록을 열고 **프레임워크 종속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-258">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="99bd1-259">**이동식**을 **대상 런타임**으로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-259">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="99bd1-260">배포 시 추가 파일을 제거해야 하는 경우 **파일 게시 옵션**을 열고 대상에서 추가 파일을 제거하는 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="99bd1-261">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-261">Select **Save**.</span></span>
1. <span data-ttu-id="99bd1-262">게시 마법사의 나머지 프롬프트를 따라 새 사이트를 작성하거나 기존 사이트를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="99bd1-263">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99bd1-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="99bd1-264">프로젝트 파일에서 [런타임 식별자(RID)](/dotnet/core/rid-catalog)를 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-264">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="99bd1-265">명령 셸에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성에 있는 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-265">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="99bd1-266">다음 예제에서는 앱이 프레임워크 종속 앱으로 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-266">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="99bd1-267">*bin/Release/{TARGET FRAMEWORK}/publish* 디렉터리의 콘텐츠를 App Service의 사이트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-267">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="99bd1-268">로컬 하드 드라이브 또는 네트워크 공유에서 *게시* 폴더 콘텐츠를 [Kudu](https://github.com/projectkudu/kudu/wiki) 콘솔의 App Service로 직접 끌어오는 경우에는 Kudu 콘솔의 `D:\home\site\wwwroot` 폴더로 파일을 끕니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-268">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="99bd1-269">자체 포함된 앱 배포</span><span class="sxs-lookup"><span data-stu-id="99bd1-269">Deploy the app self-contained</span></span>

<span data-ttu-id="99bd1-270">[SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)에는 Visual Studio 또는 .NET Core CLI를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-270">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99bd1-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99bd1-271">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="99bd1-272">Visual Studio 도구 모음에서 **빌드** > **{애플리케이션 이름} 게시**를 선택하거나 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-272">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="99bd1-273">**공개 대상 선택** 대화 상자에서 **App Service**가 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-273">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="99bd1-274">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-274">Select **Advanced**.</span></span> <span data-ttu-id="99bd1-275">**게시** 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-275">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="99bd1-276">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="99bd1-276">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="99bd1-277">**릴리스** 구성이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-277">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="99bd1-278">**배포 모드** 드롭다운 목록을 열고 **자체 포함**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-278">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="99bd1-279">**대상 런타임** 드롭다운 목록에서 대상 런타임을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-279">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="99bd1-280">기본값은 `win-x86`입니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-280">The default is `win-x86`.</span></span>
   * <span data-ttu-id="99bd1-281">배포 시 추가 파일을 제거해야 하는 경우 **파일 게시 옵션**을 열고 대상에서 추가 파일을 제거하는 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-281">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="99bd1-282">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-282">Select **Save**.</span></span>
1. <span data-ttu-id="99bd1-283">게시 마법사의 나머지 프롬프트를 따라 새 사이트를 작성하거나 기존 사이트를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-283">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="99bd1-284">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99bd1-284">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="99bd1-285">프로젝트 파일에서 하나 이상의 [RID(런타임 ID)](/dotnet/core/rid-catalog)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-285">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="99bd1-286">단일 RID에 `<RuntimeIdentifier>`(단수)을 사용하거나, `<RuntimeIdentifiers>`(복수)를 사용하여 세미콜론으로 구분된 RID 목록을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-286">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="99bd1-287">다음 예제에서는 `win-x86` RID가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-287">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="99bd1-288">명령 셸에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 호스트의 런타임에 대한 릴리스 구성에 있는 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-288">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="99bd1-289">다음 예제에서 앱은 `win-x86` RID에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-289">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="99bd1-290">`--runtime` 옵션에 제공된 RID를 프로젝트 파일의 `<RuntimeIdentifier>`(또는 `<RuntimeIdentifiers>`) 속성에 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-290">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="99bd1-291">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* 디렉터리의 콘텐츠를 App Service의 사이트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-291">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="99bd1-292">로컬 하드 드라이브 또는 네트워크 공유에서 *게시* 폴더 콘텐츠를 Kudu 콘솔의 App Service로 직접 끌어오는 경우에는 Kudu 콘솔의 `D:\home\site\wwwroot` 폴더로 파일을 끕니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-292">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="99bd1-293">프로토콜 설정(HTTPS)</span><span class="sxs-lookup"><span data-stu-id="99bd1-293">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="99bd1-294">보안 프로토콜 바인딩을 사용하면 HTTPS를 통한 요청에 응답할 때 사용할 인증서를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-294">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="99bd1-295">바인딩을 위해서는 특정 호스트 이름에 대해 발행된 유효한 개인 인증서(*.pfx*)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-295">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="99bd1-296">자세한 내용은 [자습서: 기존 사용자 지정 SSL 인증서를 Azure App Service에 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-296">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="99bd1-297">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="99bd1-297">Transform web.config</span></span>

<span data-ttu-id="99bd1-298">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="99bd1-298">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99bd1-299">추가 자료</span><span class="sxs-lookup"><span data-stu-id="99bd1-299">Additional resources</span></span>

* [<span data-ttu-id="99bd1-300">App Service 개요</span><span class="sxs-lookup"><span data-stu-id="99bd1-300">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="99bd1-301">Azure App Service: .NET 앱을 호스트하기에 가장 좋은 서비스(55분 개요 동영상)</span><span class="sxs-lookup"><span data-stu-id="99bd1-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="99bd1-302">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="99bd1-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="99bd1-303">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="99bd1-303">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="99bd1-304">Windows Server의 Azure App Service는 [IIS(인터넷 정보 서비스)](https://www.iis.net/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-304">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="99bd1-305">다음 항목은 기본 IIS 기술과 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="99bd1-305">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="99bd1-306">Windows Server - 현재 및 이전 릴리스에 대한 IT 관리자 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="99bd1-306">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
