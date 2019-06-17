---
title: Azure App Service에 ASP.NET Core 앱 배포
author: guardrex
description: 이 문서에는 Azure 호스트 및 배포 리소스의 링크가 포함됩니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 5daefde13310ebeb232ef4c8886b12ad78182e50
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048249"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="ff408-103">Azure App Service에 ASP.NET Core 앱 배포</span><span class="sxs-lookup"><span data-stu-id="ff408-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="ff408-104">[Azure App Service](https://azure.microsoft.com/services/app-service/)는 ASP.NET Core를 비롯한 웹앱을 호스트하기 위한 [Microsoft 클라우드 컴퓨팅 플랫폼 서비스](https://azure.microsoft.com/)입니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="ff408-105">유용한 리소스</span><span class="sxs-lookup"><span data-stu-id="ff408-105">Useful resources</span></span>

<span data-ttu-id="ff408-106">[App Service 설명서](/azure/app-service/)에서는 Azure 앱 설명서, 자습서, 샘플, 방법 가이드 및 기타 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="ff408-107">ASP.NET Core 앱 호스트와 관련하여 두 가지 주목할 만한 자습서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="ff408-108">Azure에서 ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="ff408-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="ff408-109">Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 Windows의 Azure App Service에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="ff408-110">App Service on Linux에서 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="ff408-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="ff408-111">명령줄을 사용하여 ASP.NET Core 웹앱을 만들고 Linux의 Azure App Service에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="ff408-112">다음 문서는 ASP.NET Core 설명서에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-112">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="ff408-113">Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 앱을 게시하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-113">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="ff408-114">Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 위한 Git을 사용하여 Azure App Service에 배포하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-114">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="ff408-115">첫 번째 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="ff408-115">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="ff408-116">ASP.NET Core 앱에 대한 CI 빌드를 설정하고 Azure App Service에 대한 연속 배포 릴리스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-116">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="ff408-117">Azure Web App 샌드박스</span><span class="sxs-lookup"><span data-stu-id="ff408-117">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="ff408-118">Azure 앱 플랫폼에서 적용하는 Azure App Service 런타임 실행 제한 사항을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-118">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="ff408-119">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="ff408-119">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="ff408-120">플랫폼</span><span class="sxs-lookup"><span data-stu-id="ff408-120">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ff408-121">64비트(x64) 및 32비트(x86) 앱은 Azure App Service에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-121">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="ff408-122">App Service에서 사용 가능한 [.NET Core SDK](/dotnet/core/sdk)는 32비트이지만 [Kudu](https://github.com/projectkudu/kudu/wiki) 콘솔 또는 [Visual Studio 게시 프로필이나 CLI 명령을 사용하는 MSDeploy](xref:host-and-deploy/visual-studio-publish-profiles)를 통해 64비트 앱을 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-122">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or via [MSDeploy with a Visual Studio publish profile or CLI command](xref:host-and-deploy/visual-studio-publish-profiles).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ff408-123">원시 종속 항목을 지원하는 앱의 경우 32비트(x86) 앱의 런타임이 Azure App Service에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-123">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="ff408-124">App Service에서 사용 가능한 [.NET Core SDK](/dotnet/core/sdk)는 32비트입니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-124">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

### <a name="packages"></a><span data-ttu-id="ff408-125">패키지</span><span class="sxs-lookup"><span data-stu-id="ff408-125">Packages</span></span>

<span data-ttu-id="ff408-126">Azure App Service에 배포된 앱에 대한 자동 로깅 기능을 제공하려면 다음 NuGet 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-126">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="ff408-127">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/)은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)을 사용하여 Azure App Service와 ASP.NET Core의 강화 통합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-127">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="ff408-128">추가된 로깅 기능은 `Microsoft.AspNetCore.AzureAppServicesIntegration` 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-128">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="ff408-129">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/)은 [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics)를 실행하여 `Microsoft.Extensions.Logging.AzureAppServices` 패키지의 Azure App Service 진단 로깅 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-129">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="ff408-130">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)는 Azure App Service 진단 로그 및 로그 스트리밍 기능을 지원하는 로거 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-130">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="ff408-131">이전의 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-131">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="ff408-132">.NET Framework를 대상으로 하거나 `Microsoft.AspNetCore.App` 메타패키지를 참조하는 앱은 앱 프로젝트 파일의 개별 패키지를 명시적으로 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-132">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="ff408-133">Azure Portal을 사용하여 앱 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="ff408-133">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="ff408-134">Azure Portal의 앱 설정을 사용하면 앱의 환경 변수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-134">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="ff408-135">환경 변수는 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)가 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-135">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="ff408-136">Azure Portal에서 앱 설정을 만들거나 수정하고**저장** 단추를 선택하면 Azure 앱이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-136">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="ff408-137">서비스를 다시 시작한 후에 환경 변수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-137">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff408-138">앱이 [일반 호스트](xref:fundamentals/host/generic-host)를 사용하는 경우에는 기본적으로 환경 변수가 앱 구성으로 로드되지 않으며 개발자가 구성 공급자를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-138">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="ff408-139">개발자가 구성 공급자를 추가할 때 환경 변수 접두사를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-139">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="ff408-140">자세한 내용은 <xref:fundamentals/host/generic-host> 및 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-140">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="ff408-141">앱이 [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)를 사용하여 호스트를 빌드하는 경우 호스트를 구성하는 환경 변수는 `ASPNETCORE_` 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-141">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="ff408-142">자세한 내용은 <xref:fundamentals/host/web-host> 및 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-142">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ff408-143">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="ff408-143">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ff408-144">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-144">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="ff408-145">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-145">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="ff408-146">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-146">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="ff408-147">모니터링 및 로깅</span><span class="sxs-lookup"><span data-stu-id="ff408-147">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff408-148">App Service에 배포된 ASP.NET Core 앱은 자동으로 App Service 확장인 **ASP.NET Core 로깅 통합**을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-148">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="ff408-149">확장을 사용하면 Azure App Service에서 ASP.NET Core 앱에서 로깅 통합이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-149">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff408-150">App Service에 배포된 ASP.NET Core 앱은 자동으로 App Service 확장인 **ASP.NET Core 로깅 확장**을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-150">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="ff408-151">확장을 사용하면 Azure App Service에서 ASP.NET Core 앱에서 로깅 통합이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-151">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="ff408-152">모니터링, 로깅 및 문제 해결에 대한 자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-152">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="ff408-153">Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="ff408-153">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="ff408-154">앱 및 App Service 계획의 할당량 및 메트릭을 검토하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-154">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="ff408-155">Azure App Service에서 앱에 대한 진단 로깅 사용</span><span class="sxs-lookup"><span data-stu-id="ff408-155">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="ff408-156">HTTP 상태 코드, 실패한 요청 및 웹 서버 활동에 대한 진단 로깅을 활성화하고 액세스하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-156">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="ff408-157">ASP.NET Core 앱에서 오류를 처리하기 위한 일반적인 접근법을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-157">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-apps/troubleshoot>  
<span data-ttu-id="ff408-158">ASP.NET Core 앱을 사용하는 Azure App Service 배포에 대한 문제 진단 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-158">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="ff408-159">Azure App Service/IIS에서 호스트하는 앱의 일반적인 배포 구성 오류와 문제 해결에 대한 조언을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-159">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="ff408-160">데이터 보호 키 링 및 배포 슬롯</span><span class="sxs-lookup"><span data-stu-id="ff408-160">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="ff408-161">[데이터 보호 키](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)는 *%HOME%\ASP.NET\DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-161">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="ff408-162">이 폴더는 네트워크 스토리지에서 지원하고, 앱을 호스트하는 모든 머신에서 동기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-162">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="ff408-163">저장된 키는 보호되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-163">Keys aren't protected at rest.</span></span> <span data-ttu-id="ff408-164">이 폴더는 단일 배포 슬롯에 앱의 모든 인스턴스에 대한 키 링을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-164">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="ff408-165">준비 및 프로덕션과 같은 별도의 배포 슬롯은 키 링을 공유하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-165">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="ff408-166">배포 슬롯을 바꿀 경우 데이터 보호를 사용하는 모든 시스템은 이전 슬롯 내에 있는 키 링을 사용하여 저장된 데이터의 암호를 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-166">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="ff408-167">ASP.NET 쿠키 미들웨어는 데이터 보호를 사용하여 쿠키를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-167">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="ff408-168">이로 인해 표준 ASP.NET 쿠키 미들웨어를 사용하는 앱에서 사용자가 로그아웃됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-168">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="ff408-169">슬롯에 관계없는 키 링 솔루션의 경우 다음과 같은 외부 키 링 공급자를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-169">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="ff408-170">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="ff408-170">Azure Blob Storage</span></span>
* <span data-ttu-id="ff408-171">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ff408-171">Azure Key Vault</span></span>
* <span data-ttu-id="ff408-172">SQL 저장소</span><span class="sxs-lookup"><span data-stu-id="ff408-172">SQL store</span></span>
* <span data-ttu-id="ff408-173">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="ff408-173">Redis cache</span></span>

<span data-ttu-id="ff408-174">자세한 내용은 <xref:security/data-protection/implementation/key-storage-providers>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-174">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a><span data-ttu-id="ff408-175">Azure App Service에 ASP.NET Core 미리 보기 릴리스 배포</span><span class="sxs-lookup"><span data-stu-id="ff408-175">Deploy ASP.NET Core preview release to Azure App Service</span></span>

<span data-ttu-id="ff408-176">다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-176">Use one of the following approaches:</span></span>

* <span data-ttu-id="ff408-177">[미리 보기 사이트 확장을 설치](#install-the-preview-site-extension)합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-177">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="ff408-178">[자체 포함된 앱을 배포](#deploy-the-app-self-contained)합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-178">[Deploy the app self-contained](#deploy-the-app-self-contained).</span></span>
* <span data-ttu-id="ff408-179">[Web Apps for Containers에서 Docker를 사용](#use-docker-with-web-apps-for-containers)합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-179">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="ff408-180">미리 보기 사이트 확장 설치</span><span class="sxs-lookup"><span data-stu-id="ff408-180">Install the preview site extension</span></span>

<span data-ttu-id="ff408-181">미리 보기 사이트 확장을 사용하는 데 문제가 발생하는 경우 [aspnet/AspNetCore 문제](https://github.com/aspnet/AspNetCore/issues)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-181">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="ff408-182">Azure Portal에서 App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-182">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="ff408-183">웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-183">Select the web app.</span></span>
1. <span data-ttu-id="ff408-184">검색 상자에 "ex"를 입력하여 "확장"으로 필터링하거나 관리 도구 목록을 아래로 스크롤합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-184">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="ff408-185">**확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-185">Select **Extensions**.</span></span>
1. <span data-ttu-id="ff408-186">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-186">Select **Add**.</span></span>
1. <span data-ttu-id="ff408-187">목록에서 **ASP.NET Core {X.Y}({x64|x86}) 런타임** 확장을 선택합니다. 여기서 `{X.Y}`는 ASP.NET Core 미리 보기 버전이며 `{x64|x86}`은 플랫폼을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-187">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="ff408-188">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-188">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="ff408-189">확장을 설치하려면 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-189">Select **OK** to install the extension.</span></span>

<span data-ttu-id="ff408-190">작업이 완료되면 최신.NET Core 미리 보기가 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-190">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="ff408-191">설치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-191">Verify the installation:</span></span>

1. <span data-ttu-id="ff408-192">**고급 도구**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-192">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="ff408-193">**고급 도구**에서 **Go**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-193">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="ff408-194">**디버그 콘솔** > **PowerShell** 메뉴 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-194">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="ff408-195">PowerShell 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-195">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="ff408-196">명령에서 `{X.Y}`를 ASP.NET Core 런타임 버전으로 대체하고, `{PLATFORM}`을 플랫폼으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-196">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="ff408-197">x64 미리 보기 런타임이 설치된 경우 명령이 `True`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-197">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="ff408-198">A 시리즈 컴퓨팅 또는 더 나은 호스트 계층에서 호스트되는 앱이 필요한 경우 Azure Portal의 앱 설정에서 App Services 앱의 플랫폼 아키텍처(x86/x64)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-198">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="ff408-199">앱이 in-process 모드에서 실행되고 플랫폼 아키텍처가 64비트(x64)에 대해 구성되는 경우 ASP.NET Core 모듈은 64비트 미리 보기 런타임을 사용합니다(있는 경우).</span><span class="sxs-lookup"><span data-stu-id="ff408-199">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="ff408-200">**ASP.NET Core {X.Y}(x64) 런타임** 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-200">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="ff408-201">x64 미리 보기 런타임을 설치한 후에 Kudu PowerShell 명령 창에서 다음 명령을 실행하여 설치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-201">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="ff408-202">명령에서 `{X.Y}`에 대한 ASP.NET Core 런타임 버전을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-202">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="ff408-203">x64 미리 보기 런타임이 설치된 경우 명령이 `True`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-203">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="ff408-204">**ASP.NET Core 확장**을 사용하면 Azure 로깅을 사용하는 등 Azure App Services에서 ASP.NET Core에 대한 추가 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-204">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="ff408-205">Visual Studio에서 배포할 경우 확장은 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-205">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="ff408-206">확장이 설치되지 않은 경우 앱에서 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-206">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="ff408-207">**ARM 템플릿에서 미리 보기 사이트 확장 사용**</span><span class="sxs-lookup"><span data-stu-id="ff408-207">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="ff408-208">ARM 템플릿을 사용하여 앱을 만들고 배포하는 경우 `siteextensions` 리소스 형식을 사용하여 웹앱에 사이트 확장을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-208">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="ff408-209">예:</span><span class="sxs-lookup"><span data-stu-id="ff408-209">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="ff408-210">자체 포함된 앱 배포</span><span class="sxs-lookup"><span data-stu-id="ff408-210">Deploy the app self-contained</span></span>

<span data-ttu-id="ff408-211">미리 보기 런타임을 대상으로 하는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)는 배포 시 미리 보기 런타임을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-211">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="ff408-212">자체 포함된 앱을 배포하는 경우:</span><span class="sxs-lookup"><span data-stu-id="ff408-212">When deploying a self-contained app:</span></span>

* <span data-ttu-id="ff408-213">Azure App Service의 사이트에는 [미리 보기 사이트 확장](#install-the-preview-site-extension)이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-213">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="ff408-214">앱은 [FDD(프레임워크 종속 배포)](/dotnet/core/deploying#framework-dependent-deployments-fdd)에 대해 게시할 때와 다른 접근 방식으로 공개해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-214">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

#### <a name="publish-from-visual-studio"></a><span data-ttu-id="ff408-215">Visual Studio에서 게시</span><span class="sxs-lookup"><span data-stu-id="ff408-215">Publish from Visual Studio</span></span>

1. <span data-ttu-id="ff408-216">Visual Studio 도구 모음에서 **빌드** >  **{애플리케이션 이름} 게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-216">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar.</span></span>
1. <span data-ttu-id="ff408-217">**공개 대상 선택** 대화 상자에서 **App Service**가 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-217">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="ff408-218">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-218">Select **Advanced**.</span></span> <span data-ttu-id="ff408-219">**게시** 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-219">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="ff408-220">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="ff408-220">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="ff408-221">**릴리스** 구성이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-221">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="ff408-222">**배포 모드** 드롭다운 목록을 열고 **자체 포함**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-222">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="ff408-223">**대상 런타임** 드롭다운 목록에서 대상 런타임을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-223">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="ff408-224">기본값은 `win-x86`입니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-224">The default is `win-x86`.</span></span>
   * <span data-ttu-id="ff408-225">배포 시 추가 파일을 제거해야 하는 경우 **파일 게시 옵션**을 열고 대상에서 추가 파일을 제거하는 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-225">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="ff408-226">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-226">Select **Save**.</span></span>
1. <span data-ttu-id="ff408-227">게시 마법사의 나머지 프롬프트를 따라 새 사이트를 작성하거나 기존 사이트를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-227">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

#### <a name="publish-using-command-line-interface-cli-tools"></a><span data-ttu-id="ff408-228">CLI(명령줄 인터페이스) 도구를 사용하여 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-228">Publish using command-line interface (CLI) tools</span></span>

1. <span data-ttu-id="ff408-229">프로젝트 파일에서 하나 이상의 [RID(런타임 ID)](/dotnet/core/rid-catalog)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-229">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="ff408-230">단일 RID에 `<RuntimeIdentifier>`(단수)을 사용하거나, `<RuntimeIdentifiers>`(복수)를 사용하여 세미콜론으로 구분된 RID 목록을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-230">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="ff408-231">다음 예제에서는 `win-x86` RID가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-231">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>netcoreapp2.1</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ff408-232">명령 셸에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 호스트의 런타임에 대한 릴리스 구성에 있는 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-232">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ff408-233">다음 예제에서 앱은 `win-x86` RID에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-233">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="ff408-234">`--runtime` 옵션에 제공된 RID를 프로젝트 파일의 `<RuntimeIdentifier>`(또는 `<RuntimeIdentifiers>`) 속성에 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-234">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```

1. <span data-ttu-id="ff408-235">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* 디렉터리의 콘텐츠를 App Service의 사이트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-235">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="ff408-236">Web Apps for Containers에서 Docker 사용</span><span class="sxs-lookup"><span data-stu-id="ff408-236">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="ff408-237">[Docker 허브](https://hub.docker.com/r/microsoft/aspnetcore/)에는 최신 미리 보기 Docker 이미지가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-237">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="ff408-238">이미지는 기본 이미지로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-238">The images can be used as a base image.</span></span> <span data-ttu-id="ff408-239">이미지를 사용하고 일반적으로 Web App for Containers에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-239">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="protocol-settings-https"></a><span data-ttu-id="ff408-240">프로토콜 설정(HTTPS)</span><span class="sxs-lookup"><span data-stu-id="ff408-240">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="ff408-241">보안 프로토콜 바인딩을 사용하면 HTTPS를 통한 요청에 응답할 때 사용할 인증서를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-241">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="ff408-242">바인딩을 위해서는 특정 호스트 이름에 대해 발행된 유효한 개인 인증서( *.pfx*)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-242">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="ff408-243">자세한 내용은 [자습서: 기존 사용자 지정 SSL 인증서를 Azure App Service에 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-243">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="ff408-244">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="ff408-244">Transform web.config</span></span>

<span data-ttu-id="ff408-245">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff408-245">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff408-246">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ff408-246">Additional resources</span></span>

* [<span data-ttu-id="ff408-247">App Service 개요</span><span class="sxs-lookup"><span data-stu-id="ff408-247">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="ff408-248">Azure App Service: .NET 앱을 호스트하기에 가장 좋은 서비스(55분 개요 동영상)</span><span class="sxs-lookup"><span data-stu-id="ff408-248">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="ff408-249">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="ff408-249">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="ff408-250">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="ff408-250">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="ff408-251">Windows Server의 Azure App Service는 [IIS(인터넷 정보 서비스)](https://www.iis.net/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-251">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="ff408-252">다음 항목은 기본 IIS 기술과 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff408-252">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="ff408-253">Windows Server - 현재 및 이전 릴리스에 대한 IT 관리자 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="ff408-253">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
