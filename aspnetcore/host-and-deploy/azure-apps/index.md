---
title: Azure App Service에 ASP.NET Core 앱 배포
author: guardrex
description: 이 문서에는 Azure 호스트 및 배포 리소스의 링크가 포함됩니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 5da32b5fd1026263f721db442b2676d45b239b8d
ms.sourcegitcommit: 2d4c1732c4866ed26b83da35f7bc2ad021a9c701
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815592"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="492c8-103">Azure App Service에 ASP.NET Core 앱 배포</span><span class="sxs-lookup"><span data-stu-id="492c8-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="492c8-104">[Azure App Service](https://azure.microsoft.com/services/app-service/)는 ASP.NET Core를 비롯한 웹앱을 호스트하기 위한 [Microsoft 클라우드 컴퓨팅 플랫폼 서비스](https://azure.microsoft.com/)입니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="492c8-105">유용한 리소스</span><span class="sxs-lookup"><span data-stu-id="492c8-105">Useful resources</span></span>

<span data-ttu-id="492c8-106">[App Service 설명서](/azure/app-service/)에서는 Azure 앱 설명서, 자습서, 샘플, 방법 가이드 및 기타 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="492c8-107">ASP.NET Core 앱 호스트와 관련하여 두 가지 주목할 만한 자습서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="492c8-108">Azure에서 ASP.NET Core 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="492c8-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="492c8-109">Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 Windows의 Azure App Service에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="492c8-110">App Service on Linux에서 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="492c8-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="492c8-111">명령줄을 사용하여 ASP.NET Core 웹앱을 만들고 Linux의 Azure App Service에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="492c8-112">다음 문서는 ASP.NET Core 설명서에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-112">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="492c8-113">Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 앱을 게시하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-113">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="492c8-114">Visual Studio를 사용하여 ASP.NET Core 웹앱을 만들고 연속 배포를 위한 Git을 사용하여 Azure App Service에 배포하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-114">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="492c8-115">첫 번째 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="492c8-115">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="492c8-116">ASP.NET Core 앱에 대한 CI 빌드를 설정하고 Azure App Service에 대한 연속 배포 릴리스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-116">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="492c8-117">Azure Web App 샌드박스</span><span class="sxs-lookup"><span data-stu-id="492c8-117">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="492c8-118">Azure 앱 플랫폼에서 적용하는 Azure App Service 런타임 실행 제한 사항을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-118">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="492c8-119">ASP.NET Core 프로젝트를 사용하여 경고 및 오류를 이해하고 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-119">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="492c8-120">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="492c8-120">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="492c8-121">플랫폼</span><span class="sxs-lookup"><span data-stu-id="492c8-121">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="492c8-122">64비트(x64) 및 32비트(x86) 앱은 Azure App Service에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-122">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="492c8-123">App Service에서 사용 가능한 [.NET Core SDK](/dotnet/core/sdk)는 32비트이지만 [Kudu](https://github.com/projectkudu/kudu/wiki) 콘솔 또는 Visual Studio의 게시 프로세스를 사용하여 로컬로 빌드된 64비트 앱을 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-123">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="492c8-124">자세한 내용은 [앱 게시 및 배포](#publish-and-deploy-the-app) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-124">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="492c8-125">원시 종속 항목을 지원하는 앱의 경우 32비트(x86) 앱의 런타임이 Azure App Service에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-125">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="492c8-126">App Service에서 사용 가능한 [.NET Core SDK](/dotnet/core/sdk)는 32비트입니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-126">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="492c8-127">.NET Core 런타임 및 .NET Core SDK에 대한 정보와 같은 .NET Core 프레임워크 구성 요소 및 배포 방법에 대한 자세한 내용은 [.NET Core: 컴퍼지션 정보](/dotnet/core/about#composition)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-127">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="492c8-128">패키지</span><span class="sxs-lookup"><span data-stu-id="492c8-128">Packages</span></span>

<span data-ttu-id="492c8-129">Azure App Service에 배포된 앱에 대한 자동 로깅 기능을 제공하려면 다음 NuGet 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-129">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="492c8-130">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/)은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)을 사용하여 Azure App Service와 ASP.NET Core의 강화 통합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-130">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="492c8-131">추가된 로깅 기능은 `Microsoft.AspNetCore.AzureAppServicesIntegration` 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-131">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="492c8-132">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/)은 [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics)를 실행하여 `Microsoft.Extensions.Logging.AzureAppServices` 패키지의 Azure App Service 진단 로깅 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-132">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="492c8-133">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)는 Azure App Service 진단 로그 및 로그 스트리밍 기능을 지원하는 로거 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-133">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="492c8-134">이전의 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-134">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="492c8-135">.NET Framework를 대상으로 하거나 `Microsoft.AspNetCore.App` 메타패키지를 참조하는 앱은 앱 프로젝트 파일의 개별 패키지를 명시적으로 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-135">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="492c8-136">Azure Portal을 사용하여 앱 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="492c8-136">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="492c8-137">Azure Portal의 앱 설정을 사용하면 앱의 환경 변수를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-137">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="492c8-138">환경 변수는 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)가 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-138">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="492c8-139">Azure Portal에서 앱 설정을 만들거나 수정하고**저장** 단추를 선택하면 Azure 앱이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-139">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="492c8-140">서비스를 다시 시작한 후에 환경 변수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-140">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="492c8-141">앱이 [일반 호스트](xref:fundamentals/host/generic-host)를 사용하는 경우에는 기본적으로 환경 변수가 앱 구성으로 로드되지 않으며 개발자가 구성 공급자를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-141">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="492c8-142">개발자가 구성 공급자를 추가할 때 환경 변수 접두사를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-142">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="492c8-143">자세한 내용은 <xref:fundamentals/host/generic-host> 및 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-143">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="492c8-144">앱이 [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)를 사용하여 호스트를 빌드하는 경우 호스트를 구성하는 환경 변수는 `ASPNETCORE_` 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-144">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="492c8-145">자세한 내용은 <xref:fundamentals/host/web-host> 및 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-145">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="492c8-146">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="492c8-146">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="492c8-147">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-147">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="492c8-148">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-148">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="492c8-149">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-149">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="492c8-150">모니터링 및 로깅</span><span class="sxs-lookup"><span data-stu-id="492c8-150">Monitoring and logging</span></span>

<span data-ttu-id="492c8-151">Azure App Service는 ASP.NET Core 앱에 대한 로깅 통합을 사용할 수 있게 하는 **ASP.NET Core 로깅 확장**을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-151">Azure App Service offers the **ASP.NET Core Logging Extensions**, which enable logging integration for ASP.NET Core apps.</span></span> <span data-ttu-id="492c8-152">App Service에 확장명을 자동으로 추가하려면 Visual Studio의 **게시** 프로세스를 **App Service** 게시 프로필과 함께 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-152">To automatically add the extension to an App Service, use Visual Studio's **Publish** process with an **App Service** publish profile.</span></span> <span data-ttu-id="492c8-153">Visual Studio를 사용하여 앱을 배포하지 않는 경우 App Service의 **개발 도구** > **확장** 대화 상자를 통해 Azure Portal에 확장을 수동으로 설치하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-153">When not using Visual Studio to deploy an app, manually install the extension in the Azure Portal via the App Service's **Development Tools** > **Extensions** dialog.</span></span>

<span data-ttu-id="492c8-154">모니터링, 로깅 및 문제 해결에 대한 자세한 내용은 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-154">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="492c8-155">Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="492c8-155">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="492c8-156">앱 및 App Service 계획의 할당량 및 메트릭을 검토하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-156">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="492c8-157">Azure App Service에서 앱에 대한 진단 로깅 사용</span><span class="sxs-lookup"><span data-stu-id="492c8-157">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="492c8-158">HTTP 상태 코드, 실패한 요청 및 웹 서버 활동에 대한 진단 로깅을 활성화하고 액세스하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-158">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="492c8-159">ASP.NET Core 앱에서 오류를 처리하기 위한 일반적인 접근법을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-159">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="492c8-160">ASP.NET Core 앱을 사용하는 Azure App Service 배포에 대한 문제 진단 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-160">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="492c8-161">Azure App Service/IIS에서 호스트하는 앱의 일반적인 배포 구성 오류와 문제 해결에 대한 조언을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-161">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="492c8-162">데이터 보호 키 링 및 배포 슬롯</span><span class="sxs-lookup"><span data-stu-id="492c8-162">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="492c8-163">[데이터 보호 키](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)는 *%HOME%\ASP.NET\DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-163">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="492c8-164">이 폴더는 네트워크 스토리지에서 지원하고, 앱을 호스트하는 모든 머신에서 동기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-164">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="492c8-165">저장된 키는 보호되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-165">Keys aren't protected at rest.</span></span> <span data-ttu-id="492c8-166">이 폴더는 단일 배포 슬롯에 앱의 모든 인스턴스에 대한 키 링을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-166">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="492c8-167">준비 및 프로덕션과 같은 별도의 배포 슬롯은 키 링을 공유하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-167">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="492c8-168">배포 슬롯을 바꿀 경우 데이터 보호를 사용하는 모든 시스템은 이전 슬롯 내에 있는 키 링을 사용하여 저장된 데이터의 암호를 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-168">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="492c8-169">ASP.NET 쿠키 미들웨어는 데이터 보호를 사용하여 쿠키를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-169">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="492c8-170">이로 인해 표준 ASP.NET 쿠키 미들웨어를 사용하는 앱에서 사용자가 로그아웃됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-170">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="492c8-171">슬롯에 관계없는 키 링 솔루션의 경우 다음과 같은 외부 키 링 공급자를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-171">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="492c8-172">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="492c8-172">Azure Blob Storage</span></span>
* <span data-ttu-id="492c8-173">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="492c8-173">Azure Key Vault</span></span>
* <span data-ttu-id="492c8-174">SQL 저장소</span><span class="sxs-lookup"><span data-stu-id="492c8-174">SQL store</span></span>
* <span data-ttu-id="492c8-175">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="492c8-175">Redis cache</span></span>

<span data-ttu-id="492c8-176">자세한 내용은 <xref:security/data-protection/implementation/key-storage-providers>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-176">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a><span data-ttu-id="492c8-177">Azure App Service에 ASP.NET Core 미리 보기 릴리스 배포</span><span class="sxs-lookup"><span data-stu-id="492c8-177">Deploy ASP.NET Core preview release to Azure App Service</span></span>

<span data-ttu-id="492c8-178">앱이 .NET Core의 미리 보기 릴리스를 사용하는 경우 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-178">Use one of the following approaches if the app relies on a preview release of .NET Core:</span></span>

* <span data-ttu-id="492c8-179">[미리 보기 사이트 확장을 설치](#install-the-preview-site-extension)합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-179">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="492c8-180">[자체 포함 미리 보기 앱을 배포합니다](#deploy-a-self-contained-preview-app).</span><span class="sxs-lookup"><span data-stu-id="492c8-180">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="492c8-181">[Web Apps for Containers에서 Docker를 사용](#use-docker-with-web-apps-for-containers)합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-181">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="492c8-182">미리 보기 사이트 확장 설치</span><span class="sxs-lookup"><span data-stu-id="492c8-182">Install the preview site extension</span></span>

<span data-ttu-id="492c8-183">미리 보기 사이트 확장을 사용하는 데 문제가 발생하는 경우 [aspnet/AspNetCore 문제](https://github.com/aspnet/AspNetCore/issues)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-183">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="492c8-184">Azure Portal에서 App Service로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-184">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="492c8-185">웹앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-185">Select the web app.</span></span>
1. <span data-ttu-id="492c8-186">검색 상자에 "ex"를 입력하여 "확장"으로 필터링하거나 관리 도구 목록을 아래로 스크롤합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-186">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="492c8-187">**확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-187">Select **Extensions**.</span></span>
1. <span data-ttu-id="492c8-188">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-188">Select **Add**.</span></span>
1. <span data-ttu-id="492c8-189">목록에서 **ASP.NET Core {X.Y}({x64|x86}) 런타임** 확장을 선택합니다. 여기서 `{X.Y}`는 ASP.NET Core 미리 보기 버전이며 `{x64|x86}`은 플랫폼을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-189">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="492c8-190">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-190">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="492c8-191">확장을 설치하려면 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-191">Select **OK** to install the extension.</span></span>

<span data-ttu-id="492c8-192">작업이 완료되면 최신.NET Core 미리 보기가 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-192">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="492c8-193">설치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-193">Verify the installation:</span></span>

1. <span data-ttu-id="492c8-194">**고급 도구**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-194">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="492c8-195">**고급 도구**에서 **Go**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-195">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="492c8-196">**디버그 콘솔** > **PowerShell** 메뉴 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-196">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="492c8-197">PowerShell 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-197">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="492c8-198">명령에서 `{X.Y}`를 ASP.NET Core 런타임 버전으로 대체하고, `{PLATFORM}`을 플랫폼으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-198">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="492c8-199">x64 미리 보기 런타임이 설치된 경우 명령이 `True`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-199">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="492c8-200">A 시리즈 컴퓨팅 또는 더 나은 호스트 계층에서 호스트되는 앱이 필요한 경우 Azure Portal의 앱 설정에서 App Services 앱의 플랫폼 아키텍처(x86/x64)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-200">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="492c8-201">앱이 in-process 모드에서 실행되고 플랫폼 아키텍처가 64비트(x64)에 대해 구성되는 경우 ASP.NET Core 모듈은 64비트 미리 보기 런타임을 사용합니다(있는 경우).</span><span class="sxs-lookup"><span data-stu-id="492c8-201">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="492c8-202">**ASP.NET Core {X.Y}(x64) 런타임** 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-202">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="492c8-203">x64 미리 보기 런타임을 설치한 후에 Kudu PowerShell 명령 창에서 다음 명령을 실행하여 설치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-203">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="492c8-204">명령에서 `{X.Y}`에 대한 ASP.NET Core 런타임 버전을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-204">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="492c8-205">x64 미리 보기 런타임이 설치된 경우 명령이 `True`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-205">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="492c8-206">**ASP.NET Core 확장**을 사용하면 Azure 로깅을 사용하는 등 Azure App Services에서 ASP.NET Core에 대한 추가 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-206">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="492c8-207">Visual Studio에서 배포할 경우 확장은 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-207">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="492c8-208">확장이 설치되지 않은 경우 앱에서 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-208">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="492c8-209">**ARM 템플릿에서 미리 보기 사이트 확장 사용**</span><span class="sxs-lookup"><span data-stu-id="492c8-209">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="492c8-210">ARM 템플릿을 사용하여 앱을 만들고 배포하는 경우 `siteextensions` 리소스 형식을 사용하여 웹앱에 사이트 확장을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-210">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="492c8-211">예:</span><span class="sxs-lookup"><span data-stu-id="492c8-211">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="492c8-212">자체 포함 미리 보기 앱 배포</span><span class="sxs-lookup"><span data-stu-id="492c8-212">Deploy a self-contained preview app</span></span>

<span data-ttu-id="492c8-213">미리 보기 런타임을 대상으로 하는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)는 배포 시 미리 보기 런타임을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-213">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="492c8-214">자체 포함된 앱을 배포하는 경우:</span><span class="sxs-lookup"><span data-stu-id="492c8-214">When deploying a self-contained app:</span></span>

* <span data-ttu-id="492c8-215">Azure App Service의 사이트에는 [미리 보기 사이트 확장](#install-the-preview-site-extension)이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-215">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="492c8-216">앱은 [FDD(프레임워크 종속 배포)](/dotnet/core/deploying#framework-dependent-deployments-fdd)에 대해 게시할 때와 다른 접근 방식으로 공개해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-216">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="492c8-217">[자체 포함된 앱 배포](#deploy-the-app-self-contained) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-217">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="492c8-218">Web Apps for Containers에서 Docker 사용</span><span class="sxs-lookup"><span data-stu-id="492c8-218">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="492c8-219">[Docker 허브](https://hub.docker.com/r/microsoft/aspnetcore/)에는 최신 미리 보기 Docker 이미지가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-219">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="492c8-220">이미지는 기본 이미지로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-220">The images can be used as a base image.</span></span> <span data-ttu-id="492c8-221">이미지를 사용하고 일반적으로 Web App for Containers에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-221">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="492c8-222">앱 게시 및 배포</span><span class="sxs-lookup"><span data-stu-id="492c8-222">Publish and deploy the app</span></span>

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="492c8-223">프레임워크 종속 앱 배포</span><span class="sxs-lookup"><span data-stu-id="492c8-223">Deploy the app framework-dependent</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="492c8-224">64비트 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)의 경우:</span><span class="sxs-lookup"><span data-stu-id="492c8-224">For a 64-bit [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

* <span data-ttu-id="492c8-225">64비트 .NET Core SDK를 사용하여 64비트 앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-225">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="492c8-226">App Service의 **구성** > **일반 설정**에서 **플랫폼**을 **64비트**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-226">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="492c8-227">플랫폼 비트 수를 선택할 수 있도록 하려면 앱에서 기본 이상의 서비스 플랜을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-227">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="492c8-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="492c8-228">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="492c8-229">Visual Studio 도구 모음에서 **빌드** >  **{애플리케이션 이름} 게시**를 선택하거나 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-229">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="492c8-230">**공개 대상 선택** 대화 상자에서 **App Service**가 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-230">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="492c8-231">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-231">Select **Advanced**.</span></span> <span data-ttu-id="492c8-232">**게시** 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-232">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="492c8-233">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="492c8-233">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="492c8-234">**릴리스** 구성이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-234">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="492c8-235">**배포 모드** 드롭다운 목록을 열고 **프레임워크 종속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-235">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="492c8-236">**이동식**을 **대상 런타임**으로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-236">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="492c8-237">배포 시 추가 파일을 제거해야 하는 경우 **파일 게시 옵션**을 열고 대상에서 추가 파일을 제거하는 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-237">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="492c8-238">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-238">Select **Save**.</span></span>
1. <span data-ttu-id="492c8-239">게시 마법사의 나머지 프롬프트를 따라 새 사이트를 작성하거나 기존 사이트를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-239">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="492c8-240">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="492c8-240">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="492c8-241">프로젝트 파일에서 [런타임 식별자(RID)](/dotnet/core/rid-catalog)를 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-241">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="492c8-242">명령 셸에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성에 있는 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-242">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="492c8-243">다음 예제에서는 앱이 프레임워크 종속 앱으로 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-243">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="492c8-244">*bin/Release/{TARGET FRAMEWORK}/publish* 디렉터리의 콘텐츠를 App Service의 사이트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-244">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="492c8-245">로컬 하드 드라이브 또는 네트워크 공유에서 *게시* 폴더 콘텐츠를 [Kudu](https://github.com/projectkudu/kudu/wiki) 콘솔의 App Service로 직접 끌어오는 경우에는 Kudu 콘솔의 `D:\home\site\wwwroot` 폴더로 파일을 끕니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-245">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="492c8-246">자체 포함된 앱 배포</span><span class="sxs-lookup"><span data-stu-id="492c8-246">Deploy the app self-contained</span></span>

<span data-ttu-id="492c8-247">[자체 포함 배포(SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)에는 Visual Studio 또는 CLI(명령줄 인터페이스)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-247">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="492c8-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="492c8-248">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="492c8-249">Visual Studio 도구 모음에서 **빌드** >  **{애플리케이션 이름} 게시**를 선택하거나 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-249">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="492c8-250">**공개 대상 선택** 대화 상자에서 **App Service**가 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-250">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="492c8-251">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-251">Select **Advanced**.</span></span> <span data-ttu-id="492c8-252">**게시** 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-252">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="492c8-253">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="492c8-253">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="492c8-254">**릴리스** 구성이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-254">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="492c8-255">**배포 모드** 드롭다운 목록을 열고 **자체 포함**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-255">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="492c8-256">**대상 런타임** 드롭다운 목록에서 대상 런타임을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-256">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="492c8-257">기본값은 `win-x86`입니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-257">The default is `win-x86`.</span></span>
   * <span data-ttu-id="492c8-258">배포 시 추가 파일을 제거해야 하는 경우 **파일 게시 옵션**을 열고 대상에서 추가 파일을 제거하는 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-258">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="492c8-259">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-259">Select **Save**.</span></span>
1. <span data-ttu-id="492c8-260">게시 마법사의 나머지 프롬프트를 따라 새 사이트를 작성하거나 기존 사이트를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-260">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="492c8-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="492c8-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="492c8-262">프로젝트 파일에서 하나 이상의 [RID(런타임 ID)](/dotnet/core/rid-catalog)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-262">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="492c8-263">단일 RID에 `<RuntimeIdentifier>`(단수)을 사용하거나, `<RuntimeIdentifiers>`(복수)를 사용하여 세미콜론으로 구분된 RID 목록을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-263">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="492c8-264">다음 예제에서는 `win-x86` RID가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-264">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="492c8-265">명령 셸에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 호스트의 런타임에 대한 릴리스 구성에 있는 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-265">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="492c8-266">다음 예제에서 앱은 `win-x86` RID에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-266">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="492c8-267">`--runtime` 옵션에 제공된 RID를 프로젝트 파일의 `<RuntimeIdentifier>`(또는 `<RuntimeIdentifiers>`) 속성에 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-267">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```

1. <span data-ttu-id="492c8-268">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* 디렉터리의 콘텐츠를 App Service의 사이트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-268">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="492c8-269">로컬 하드 드라이브 또는 네트워크 공유에서 *게시* 폴더 콘텐츠를 Kudu 콘솔의 App Service로 직접 끌어오는 경우에는 Kudu 콘솔의 `D:\home\site\wwwroot` 폴더로 파일을 끕니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-269">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="492c8-270">프로토콜 설정(HTTPS)</span><span class="sxs-lookup"><span data-stu-id="492c8-270">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="492c8-271">보안 프로토콜 바인딩을 사용하면 HTTPS를 통한 요청에 응답할 때 사용할 인증서를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-271">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="492c8-272">바인딩을 위해서는 특정 호스트 이름에 대해 발행된 유효한 개인 인증서( *.pfx*)가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-272">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="492c8-273">자세한 내용은 [자습서: 기존 사용자 지정 SSL 인증서를 Azure App Service에 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-273">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="492c8-274">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="492c8-274">Transform web.config</span></span>

<span data-ttu-id="492c8-275">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="492c8-275">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="492c8-276">추가 자료</span><span class="sxs-lookup"><span data-stu-id="492c8-276">Additional resources</span></span>

* [<span data-ttu-id="492c8-277">App Service 개요</span><span class="sxs-lookup"><span data-stu-id="492c8-277">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="492c8-278">Azure App Service: .NET 앱을 호스트하기에 가장 좋은 서비스(55분 개요 동영상)</span><span class="sxs-lookup"><span data-stu-id="492c8-278">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="492c8-279">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="492c8-279">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="492c8-280">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="492c8-280">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="492c8-281">Windows Server의 Azure App Service는 [IIS(인터넷 정보 서비스)](https://www.iis.net/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-281">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="492c8-282">다음 항목은 기본 IIS 기술과 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="492c8-282">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="492c8-283">Windows Server - 현재 및 이전 릴리스에 대한 IT 관리자 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="492c8-283">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
