---
title: ASP.NET Core의 구성
author: guardrex
description: 구성 API를 사용하여 ASP.NET Core 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: df49286c3f050b8e90cb5427cf03e2b896a39294
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885567"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="6f3f5-103">ASP.NET Core의 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="6f3f5-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="6f3f5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6f3f5-105">ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다. </span><span class="sxs-lookup"><span data-stu-id="6f3f5-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="6f3f5-106">구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="6f3f5-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f3f5-107">Azure Key Vault</span></span>
* <span data-ttu-id="6f3f5-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="6f3f5-108">Azure App Configuration</span></span>
* <span data-ttu-id="6f3f5-109">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-109">Command-line arguments</span></span>
* <span data-ttu-id="6f3f5-110">사용자 지정 공급자(설치 또는 생성된)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="6f3f5-111">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="6f3f5-111">Directory files</span></span>
* <span data-ttu-id="6f3f5-112">환경 변수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-112">Environment variables</span></span>
* <span data-ttu-id="6f3f5-113">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="6f3f5-113">In-memory .NET objects</span></span>
* <span data-ttu-id="6f3f5-114">설정 파일</span><span class="sxs-lookup"><span data-stu-id="6f3f5-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f3f5-115">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 프레임워크에 의해 암시적으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f3f5-116">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="6f3f5-117">샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="6f3f5-118">‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다. </span><span class="sxs-lookup"><span data-stu-id="6f3f5-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="6f3f5-119">옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="6f3f5-120">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="6f3f5-121">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6f3f5-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="6f3f5-122">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-122">Host versus app configuration</span></span>

<span data-ttu-id="6f3f5-123">앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="6f3f5-124">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="6f3f5-125">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="6f3f5-126">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="6f3f5-127">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="6f3f5-128">기타 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-128">Other configuration</span></span>

<span data-ttu-id="6f3f5-129">이 항목에서는 *앱 구성*에 관련된 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-129">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="6f3f5-130">ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-130">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="6f3f5-131">*launch.json*/*launchSettings.json*은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-131">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="6f3f5-132"><xref:fundamentals/environments#development> 문서 내</span><span class="sxs-lookup"><span data-stu-id="6f3f5-132">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="6f3f5-133">개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체</span><span class="sxs-lookup"><span data-stu-id="6f3f5-133">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="6f3f5-134">*web.config*는 다음 항목에 설명된 서버 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-134">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="6f3f5-135">이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-135">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="6f3f5-136">기본 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-136">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f3f5-137">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-137">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="6f3f5-138">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-138">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="6f3f5-139">다음은 [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-139">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="6f3f5-140">[웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 기본 구성에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.2 버전](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-140">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="6f3f5-141">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-141">Host configuration is provided from:</span></span>
  * <span data-ttu-id="6f3f5-142">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `DOTNET_`를 접두사로 사용하는 환경 변수(예: `DOTNET_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-142">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="6f3f5-143">구성 키-값 쌍이 로드되면 접두사(`DOTNET_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-143">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="6f3f5-144">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-144">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="6f3f5-145">웹 호스트 기본 구성이 설정됩니다(`ConfigureWebHostDefaults`).</span><span class="sxs-lookup"><span data-stu-id="6f3f5-145">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="6f3f5-146">Kestrel은 웹 서버로 사용되며 앱의 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-146">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="6f3f5-147">호스트 필터링 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-147">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="6f3f5-148">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 전달된 헤더 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-148">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="6f3f5-149">IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-149">Enable IIS integration.</span></span>
* <span data-ttu-id="6f3f5-150">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-150">App configuration is provided from:</span></span>
  * <span data-ttu-id="6f3f5-151">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6f3f5-151">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="6f3f5-152">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="6f3f5-152">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="6f3f5-153">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-153">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="6f3f5-154">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-154">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="6f3f5-155">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f3f5-156">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-156">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="6f3f5-157">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-157">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="6f3f5-158">[웹호스트](xref:fundamentals/host/web-host)를 사용하는 앱에는 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-158">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="6f3f5-159">[제네릭 호스트](xref:fundamentals/host/generic-host) 사용 시 기본 구성에 대한 자세한 내용은 [이 토픽의 최신 버전](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-159">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="6f3f5-160">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-160">Host configuration is provided from:</span></span>
  * <span data-ttu-id="6f3f5-161">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `ASPNETCORE_`를 접두사로 사용하는 환경 변수(예: `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-161">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="6f3f5-162">구성 키-값 쌍이 로드되면 접두사(`ASPNETCORE_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-162">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="6f3f5-163">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-163">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="6f3f5-164">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-164">App configuration is provided from:</span></span>
  * <span data-ttu-id="6f3f5-165">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6f3f5-165">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="6f3f5-166">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="6f3f5-166">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="6f3f5-167">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-167">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="6f3f5-168">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-168">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="6f3f5-169">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-169">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="6f3f5-170">보안</span><span class="sxs-lookup"><span data-stu-id="6f3f5-170">Security</span></span>

<span data-ttu-id="6f3f5-171">중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-171">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="6f3f5-172">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-172">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="6f3f5-173">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-173">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="6f3f5-174">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-174">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="6f3f5-175">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-175">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="6f3f5-176"><xref:security/app-secrets> &ndash; 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-176"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="6f3f5-177">비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-177">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="6f3f5-178">파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-178">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="6f3f5-179">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-179">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="6f3f5-180">자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-180">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="6f3f5-181">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="6f3f5-181">Hierarchical configuration data</span></span>

<span data-ttu-id="6f3f5-182">구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-182">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="6f3f5-183">다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-183">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="6f3f5-184">파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-184">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="6f3f5-185">콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-185">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="6f3f5-186">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-186">section0:key0</span></span>
* <span data-ttu-id="6f3f5-187">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-187">section0:key1</span></span>
* <span data-ttu-id="6f3f5-188">section1:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-188">section1:key0</span></span>
* <span data-ttu-id="6f3f5-189">section1:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-189">section1:key1</span></span>

<span data-ttu-id="6f3f5-190">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-190"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="6f3f5-191">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-191">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="6f3f5-192">규칙</span><span class="sxs-lookup"><span data-stu-id="6f3f5-192">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="6f3f5-193">소스 및 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-193">Sources and providers</span></span>

<span data-ttu-id="6f3f5-194">앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-194">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="6f3f5-195">변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-195">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="6f3f5-196">예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-196">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="6f3f5-197"><xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-197"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6f3f5-198"><xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-198"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="6f3f5-199">다음 예제에서는 `_config` 필드가 구성 값에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-199">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="6f3f5-200">구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-200">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="6f3f5-201">키</span><span class="sxs-lookup"><span data-stu-id="6f3f5-201">Keys</span></span>

<span data-ttu-id="6f3f5-202">구성키는 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-202">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="6f3f5-203">키는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-203">Keys are case-insensitive.</span></span> <span data-ttu-id="6f3f5-204">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-204">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="6f3f5-205">같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-205">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="6f3f5-206">계층적 키</span><span class="sxs-lookup"><span data-stu-id="6f3f5-206">Hierarchical keys</span></span>
  * <span data-ttu-id="6f3f5-207">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-207">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="6f3f5-208">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-208">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="6f3f5-209">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-209">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="6f3f5-210">Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-210">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="6f3f5-211">비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-211">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="6f3f5-212"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-212">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="6f3f5-213">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-213">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="6f3f5-214">값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-214">Values</span></span>

<span data-ttu-id="6f3f5-215">구성 값은 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-215">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="6f3f5-216">값은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-216">Values are strings.</span></span>
* <span data-ttu-id="6f3f5-217">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-217">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="6f3f5-218">공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-218">Providers</span></span>

<span data-ttu-id="6f3f5-219">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-219">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="6f3f5-220">공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-220">Provider</span></span> | <span data-ttu-id="6f3f5-221">다음에서 구성 제공&hellip;</span><span class="sxs-lookup"><span data-stu-id="6f3f5-221">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="6f3f5-222">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="6f3f5-222">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="6f3f5-223">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f3f5-223">Azure Key Vault</span></span> |
| <span data-ttu-id="6f3f5-224">[Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-224">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="6f3f5-225">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="6f3f5-225">Azure App Configuration</span></span> |
| [<span data-ttu-id="6f3f5-226">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-226">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="6f3f5-227">명령줄 매개 변수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-227">Command-line parameters</span></span> |
| [<span data-ttu-id="6f3f5-228">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-228">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="6f3f5-229">사용자 지정 소스</span><span class="sxs-lookup"><span data-stu-id="6f3f5-229">Custom source</span></span> |
| [<span data-ttu-id="6f3f5-230">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-230">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="6f3f5-231">환경 변수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-231">Environment variables</span></span> |
| [<span data-ttu-id="6f3f5-232">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-232">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="6f3f5-233">파일(INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-233">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="6f3f5-234">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-234">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="6f3f5-235">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="6f3f5-235">Directory files</span></span> |
| [<span data-ttu-id="6f3f5-236">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-236">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="6f3f5-237">메모리 내 컬렉션</span><span class="sxs-lookup"><span data-stu-id="6f3f5-237">In-memory collections</span></span> |
| <span data-ttu-id="6f3f5-238">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="6f3f5-238">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="6f3f5-239">사용자 프로필 디렉터리의 파일</span><span class="sxs-lookup"><span data-stu-id="6f3f5-239">File in the user profile directory</span></span> |

<span data-ttu-id="6f3f5-240">시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-240">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="6f3f5-241">이 항목의 구성 공급자는 코드에서 정렬하는 순서가 아니라 사전 순으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-241">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="6f3f5-242">앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-242">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="6f3f5-243">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-243">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="6f3f5-244">파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-244">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="6f3f5-245">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f3f5-245">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="6f3f5-246">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-246">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="6f3f5-247">환경 변수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-247">Environment variables</span></span>
1. <span data-ttu-id="6f3f5-248">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-248">Command-line arguments</span></span>

<span data-ttu-id="6f3f5-249">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-249">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="6f3f5-250">`CreateDefaultBuilder`로 새 호스트 작성기가 초기화될 때 이전 공급자 시퀀스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-250">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f3f5-251">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-251">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="6f3f5-252">ConfigureHostConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-252">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="6f3f5-253">호스트 작성기를 구성하려면 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출하고 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-253">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="6f3f5-254">`ConfigureHostConfiguration`은 나중에 빌드 프로세스에서 사용하기 위해 <xref:Microsoft.Extensions.Hosting.IHostEnvironment>를 초기화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-254">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="6f3f5-255">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-255">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="6f3f5-256">UseConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-256">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="6f3f5-257">호스트 작성기를 구성하려면 구성과 함께 호스트 작성기에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-257">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

::: moniker-end

## <a name="configureappconfiguration"></a><span data-ttu-id="6f3f5-258">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="6f3f5-258">ConfigureAppConfiguration</span></span>

<span data-ttu-id="6f3f5-259">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-259">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="6f3f5-260">명령줄 인수를 사용하여 이전 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="6f3f5-260">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="6f3f5-261">명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-261">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="6f3f5-262">CreateDefaultBuilder에 의해 추가된 공급자 제거</span><span class="sxs-lookup"><span data-stu-id="6f3f5-262">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="6f3f5-263">`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-263">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="6f3f5-264">앱 시작 중 구성 사용</span><span class="sxs-lookup"><span data-stu-id="6f3f5-264">Consume configuration during app startup</span></span>

<span data-ttu-id="6f3f5-265">`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-265">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f3f5-266">자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-266">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="6f3f5-267">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-267">Command-line Configuration Provider</span></span>

<span data-ttu-id="6f3f5-268"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-268">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="6f3f5-269">명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-269">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f3f5-270">`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-270">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="6f3f5-271">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-271">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="6f3f5-272">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-272">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="6f3f5-273">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-273">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="6f3f5-274">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-274">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="6f3f5-275">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-275">Environment variables.</span></span>

<span data-ttu-id="6f3f5-276">`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-276">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="6f3f5-277">따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-277">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="6f3f5-278">`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-278">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="6f3f5-279">따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-279">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="6f3f5-280">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-280">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f3f5-281">추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-281">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="6f3f5-282">**예제**</span><span class="sxs-lookup"><span data-stu-id="6f3f5-282">**Example**</span></span>

<span data-ttu-id="6f3f5-283">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-283">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="6f3f5-284">프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-284">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="6f3f5-285">`dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).</span><span class="sxs-lookup"><span data-stu-id="6f3f5-285">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="6f3f5-286">앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-286">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="6f3f5-287">`dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-287">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="6f3f5-288">인수</span><span class="sxs-lookup"><span data-stu-id="6f3f5-288">Arguments</span></span>

<span data-ttu-id="6f3f5-289">값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-289">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="6f3f5-290">등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="6f3f5-290">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="6f3f5-291">키 접두사</span><span class="sxs-lookup"><span data-stu-id="6f3f5-291">Key prefix</span></span>               | <span data-ttu-id="6f3f5-292">예제</span><span class="sxs-lookup"><span data-stu-id="6f3f5-292">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="6f3f5-293">접두사 없음</span><span class="sxs-lookup"><span data-stu-id="6f3f5-293">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="6f3f5-294">대시 2개(`--`)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-294">Two dashes (`--`)</span></span>        | <span data-ttu-id="6f3f5-295">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="6f3f5-295">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="6f3f5-296">슬래시(`/`)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-296">Forward slash (`/`)</span></span>      | <span data-ttu-id="6f3f5-297">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="6f3f5-297">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="6f3f5-298">같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-298">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="6f3f5-299">명령 예:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-299">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="6f3f5-300">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="6f3f5-300">Switch mappings</span></span>

<span data-ttu-id="6f3f5-301">스위치 매핑은 키 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-301">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="6f3f5-302"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-302">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="6f3f5-303">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-303">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="6f3f5-304">사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-304">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="6f3f5-305">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-305">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="6f3f5-306">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-306">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="6f3f5-307">스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-307">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="6f3f5-308">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-308">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="6f3f5-309">스위치 매핑 사전을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-309">Create a switch mappings dictionary.</span></span> <span data-ttu-id="6f3f5-310">다음 예에서는 두 개의 스위치 매핑이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-310">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="6f3f5-311">호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-311">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="6f3f5-312">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-312">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="6f3f5-313">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-313">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f3f5-314">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-314">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="6f3f5-315">생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-315">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="6f3f5-316">Key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-316">Key</span></span>       | <span data-ttu-id="6f3f5-317">값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-317">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="6f3f5-318">앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-318">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="6f3f5-319">앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-319">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="6f3f5-320">Key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-320">Key</span></span>               | <span data-ttu-id="6f3f5-321">값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-321">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="6f3f5-322">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-322">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="6f3f5-323"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-323">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="6f3f5-324">환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-324">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="6f3f5-325">[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-325">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="6f3f5-326">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-326">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f3f5-327">새 호스트 작성기가 [제네릭 호스트](xref:fundamentals/host/generic-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `DOTNET_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-327">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="6f3f5-328">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-328">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f3f5-329">새 호스트 작성기가 [웹 호스트](xref:fundamentals/host/web-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `ASPNETCORE_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-329">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="6f3f5-330">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-330">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="6f3f5-331">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-331">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="6f3f5-332">접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-332">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="6f3f5-333">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="6f3f5-333">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="6f3f5-334">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-334">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="6f3f5-335">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-335">Command-line arguments.</span></span>

<span data-ttu-id="6f3f5-336">사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-336">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="6f3f5-337">이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-337">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="6f3f5-338">추가 환경 변수에서 앱 구성을 제공하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-338">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="6f3f5-339">지정된 접두사를 포함하는 환경 변수에서 다른 공급자의 값을 재정의할 수 있도록 하려면 `AddEnvironmentVariables`를 마지막으로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-339">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="6f3f5-340">**예제**</span><span class="sxs-lookup"><span data-stu-id="6f3f5-340">**Example**</span></span>

<span data-ttu-id="6f3f5-341">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-341">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="6f3f5-342">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-342">Run the sample app.</span></span> <span data-ttu-id="6f3f5-343">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-343">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="6f3f5-344">환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-344">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="6f3f5-345">이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-345">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="6f3f5-346">앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-346">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="6f3f5-347">샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-347">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="6f3f5-348">앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-348">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="6f3f5-349">접두사</span><span class="sxs-lookup"><span data-stu-id="6f3f5-349">Prefixes</span></span>

<span data-ttu-id="6f3f5-350">`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-350">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="6f3f5-351">예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-351">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="6f3f5-352">구성 키-값 쌍이 생성되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-352">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="6f3f5-353">호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-353">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="6f3f5-354">환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-354">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="6f3f5-355">**연결 문자열 접두사**</span><span class="sxs-lookup"><span data-stu-id="6f3f5-355">**Connection string prefixes**</span></span>

<span data-ttu-id="6f3f5-356">구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-356">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="6f3f5-357">즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-357">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="6f3f5-358">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="6f3f5-358">Connection string prefix</span></span> | <span data-ttu-id="6f3f5-359">공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-359">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="6f3f5-360">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-360">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="6f3f5-361">MySQL</span><span class="sxs-lookup"><span data-stu-id="6f3f5-361">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="6f3f5-362">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="6f3f5-362">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="6f3f5-363">SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f3f5-363">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="6f3f5-364">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-364">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="6f3f5-365">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-365">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="6f3f5-366">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-366">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="6f3f5-367">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="6f3f5-367">Environment variable key</span></span> | <span data-ttu-id="6f3f5-368">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="6f3f5-368">Converted configuration key</span></span> | <span data-ttu-id="6f3f5-369">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="6f3f5-369">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="6f3f5-370">구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-370">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="6f3f5-371">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-371">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="6f3f5-372">값: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f3f5-372">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="6f3f5-373">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-373">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="6f3f5-374">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f3f5-374">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="6f3f5-375">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-375">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="6f3f5-376">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f3f5-376">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="6f3f5-377">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-377">File Configuration Provider</span></span>

<span data-ttu-id="6f3f5-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="6f3f5-379">다음 구성 공급자는 특정 파일 형식 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-379">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="6f3f5-380">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-380">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="6f3f5-381">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-381">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="6f3f5-382">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-382">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="6f3f5-383">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-383">INI Configuration Provider</span></span>

<span data-ttu-id="6f3f5-384"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-384">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="6f3f5-385">INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-385">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f3f5-386">콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-386">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="6f3f5-387">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-387">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f3f5-388">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="6f3f5-388">Whether the file is optional.</span></span>
* <span data-ttu-id="6f3f5-389">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="6f3f5-389">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="6f3f5-390">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="6f3f5-390">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="6f3f5-391">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-391">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="6f3f5-392">INI 구성 파일의 일반적인 예:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-392">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="6f3f5-393">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-393">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f3f5-394">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-394">section0:key0</span></span>
* <span data-ttu-id="6f3f5-395">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-395">section0:key1</span></span>
* <span data-ttu-id="6f3f5-396">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-396">section1:subsection:key</span></span>
* <span data-ttu-id="6f3f5-397">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-397">section2:subsection0:key</span></span>
* <span data-ttu-id="6f3f5-398">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-398">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="6f3f5-399">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-399">JSON Configuration Provider</span></span>

<span data-ttu-id="6f3f5-400"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-400">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="6f3f5-401">JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-401">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f3f5-402">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-402">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f3f5-403">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="6f3f5-403">Whether the file is optional.</span></span>
* <span data-ttu-id="6f3f5-404">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="6f3f5-404">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="6f3f5-405">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="6f3f5-405">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="6f3f5-406">`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-406">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f3f5-407">이 메서드는 호출되면 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-407">The method is called to load configuration from:</span></span>

* <span data-ttu-id="6f3f5-408">*appsettings.json* &ndash; 이 파일을 먼저 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-408">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="6f3f5-409">파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-409">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="6f3f5-410">*appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-410">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="6f3f5-411">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-411">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="6f3f5-412">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-412">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="6f3f5-413">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-413">Environment variables.</span></span>
* <span data-ttu-id="6f3f5-414">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="6f3f5-414">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="6f3f5-415">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-415">Command-line arguments.</span></span>

<span data-ttu-id="6f3f5-416">JSON 구성 공급자를 먼저 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-416">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="6f3f5-417">따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-417">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="6f3f5-418">*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-418">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="6f3f5-419">**예제**</span><span class="sxs-lookup"><span data-stu-id="6f3f5-419">**Example**</span></span>

<span data-ttu-id="6f3f5-420">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-420">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="6f3f5-421">`AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-421">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="6f3f5-422">`AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-422">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="6f3f5-423">샘플 앱의 *appsettings.Development.json*의 경우 다음 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-423">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="6f3f5-424">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-424">Run the sample app.</span></span> <span data-ttu-id="6f3f5-425">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-425">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="6f3f5-426">출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-426">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="6f3f5-427">개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-427">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="6f3f5-428">프로덕션 환경에서 샘플 앱을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-428">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="6f3f5-429">*Properties/launchSettings.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-429">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="6f3f5-430">`ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-430">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="6f3f5-431">파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-431">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="6f3f5-432">*appsettings.Development.json*의 설정에서 더 이상 *appsettings.json*의 설정을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-432">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="6f3f5-433">`Logging:LogLevel:Default` 키의 로그 수준은 `Information`입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-433">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="6f3f5-434">`AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-434">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="6f3f5-435">`AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-435">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="6f3f5-436">샘플 앱의 *appsettings.Development.json*의 경우 다음 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-436">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="6f3f5-437">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-437">Run the sample app.</span></span> <span data-ttu-id="6f3f5-438">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-438">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="6f3f5-439">출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-439">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="6f3f5-440">개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-440">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="6f3f5-441">프로덕션 환경에서 샘플 앱을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-441">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="6f3f5-442">*Properties/launchSettings.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-442">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="6f3f5-443">`ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-443">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="6f3f5-444">파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-444">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="6f3f5-445">*appsettings.Development.json*의 설정에서 더 이상 *appsettings.json*의 설정을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-445">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="6f3f5-446">`Logging:LogLevel:Default` 키의 로그 수준은 `Warning`입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-446">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="6f3f5-447">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-447">XML Configuration Provider</span></span>

<span data-ttu-id="6f3f5-448"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-448">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="6f3f5-449">XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-449">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f3f5-450">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f3f5-451">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="6f3f5-451">Whether the file is optional.</span></span>
* <span data-ttu-id="6f3f5-452">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="6f3f5-452">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="6f3f5-453">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="6f3f5-453">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="6f3f5-454">구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-454">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="6f3f5-455">파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-455">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="6f3f5-456">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-456">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="6f3f5-457">XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-457">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="6f3f5-458">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-458">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f3f5-459">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-459">section0:key0</span></span>
* <span data-ttu-id="6f3f5-460">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-460">section0:key1</span></span>
* <span data-ttu-id="6f3f5-461">section1:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-461">section1:key0</span></span>
* <span data-ttu-id="6f3f5-462">section1:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-462">section1:key1</span></span>

<span data-ttu-id="6f3f5-463">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-463">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="6f3f5-464">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-464">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f3f5-465">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-465">section:section0:key:key0</span></span>
* <span data-ttu-id="6f3f5-466">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-466">section:section0:key:key1</span></span>
* <span data-ttu-id="6f3f5-467">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-467">section:section1:key:key0</span></span>
* <span data-ttu-id="6f3f5-468">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-468">section:section1:key:key1</span></span>

<span data-ttu-id="6f3f5-469">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-469">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="6f3f5-470">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-470">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f3f5-471">key:attribute</span><span class="sxs-lookup"><span data-stu-id="6f3f5-471">key:attribute</span></span>
* <span data-ttu-id="6f3f5-472">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="6f3f5-472">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="6f3f5-473">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-473">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="6f3f5-474"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-474">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="6f3f5-475">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="6f3f5-475">The key is the file name.</span></span> <span data-ttu-id="6f3f5-476">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-476">The value contains the file's contents.</span></span> <span data-ttu-id="6f3f5-477">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-477">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="6f3f5-478">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-478">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="6f3f5-479">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-479">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="6f3f5-480">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-480">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f3f5-481">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-481">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="6f3f5-482">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="6f3f5-482">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="6f3f5-483">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-483">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="6f3f5-484">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-484">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="6f3f5-485">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-485">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="6f3f5-486">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-486">Memory Configuration Provider</span></span>

<span data-ttu-id="6f3f5-487"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-487">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="6f3f5-488">메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-488">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f3f5-489">`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-489">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="6f3f5-490">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-490">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="6f3f5-491">다음 예제에서는 구성 사전이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-491">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="6f3f5-492">사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-492">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="6f3f5-493">GetValue</span><span class="sxs-lookup"><span data-stu-id="6f3f5-493">GetValue</span></span>

<span data-ttu-id="6f3f5-494">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 하나의 값을 추출하고 해당 값을 지정된 비컬렉션 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-494">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="6f3f5-495">오버로드는 기본값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-495">An overload accepts a default value.</span></span>

<span data-ttu-id="6f3f5-496">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-496">The following example:</span></span>

* <span data-ttu-id="6f3f5-497">`NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-497">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="6f3f5-498">구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-498">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="6f3f5-499">값을 `int` 형식으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-499">Types the value as an `int`.</span></span>
* <span data-ttu-id="6f3f5-500">페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-500">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="6f3f5-501">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="6f3f5-501">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="6f3f5-502">이어지는 예제에서는 다음 JSON 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-502">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="6f3f5-503">네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-503">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="6f3f5-504">파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-504">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="6f3f5-505">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-505">section0:key0</span></span>
* <span data-ttu-id="6f3f5-506">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-506">section0:key1</span></span>
* <span data-ttu-id="6f3f5-507">section1:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-507">section1:key0</span></span>
* <span data-ttu-id="6f3f5-508">section1:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-508">section1:key1</span></span>
* <span data-ttu-id="6f3f5-509">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-509">section2:subsection0:key0</span></span>
* <span data-ttu-id="6f3f5-510">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-510">section2:subsection0:key1</span></span>
* <span data-ttu-id="6f3f5-511">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-511">section2:subsection1:key0</span></span>
* <span data-ttu-id="6f3f5-512">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-512">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="6f3f5-513">GetSection</span><span class="sxs-lookup"><span data-stu-id="6f3f5-513">GetSection</span></span>

<span data-ttu-id="6f3f5-514">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-514">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="6f3f5-515">`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-515">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="6f3f5-516">`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-516">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="6f3f5-517">마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-517">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="6f3f5-518">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-518">`GetSection` never returns `null`.</span></span> <span data-ttu-id="6f3f5-519">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-519">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="6f3f5-520">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-520">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="6f3f5-521">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-521">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="6f3f5-522">GetChildren</span><span class="sxs-lookup"><span data-stu-id="6f3f5-522">GetChildren</span></span>

<span data-ttu-id="6f3f5-523">`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-523">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="6f3f5-524">있음</span><span class="sxs-lookup"><span data-stu-id="6f3f5-524">Exists</span></span>

<span data-ttu-id="6f3f5-525">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-525">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="6f3f5-526">예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-526">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="6f3f5-527">클래스에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6f3f5-527">Bind to a class</span></span>

<span data-ttu-id="6f3f5-528">‘옵션 패턴’을 사용하여 관련 설정 그룹을 나타내는 클래스에 구성을 바인딩할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="6f3f5-528">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="6f3f5-529">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-529">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="6f3f5-530">구성 값이 문자열로 반환되지만, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>를 호출하면 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-530">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="6f3f5-531">바인더는 제공된 형식의 모든 공용 읽기/쓰기 속성에 값을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-531">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="6f3f5-532">필드가 바인딩되지 **않았습니다**.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-532">Fields are **not** bound.</span></span>

<span data-ttu-id="6f3f5-533">샘플 앱은 `Starship` 모델(*Models/Starship.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-533">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6f3f5-534">샘플 앱이 JSON 구성 공급자를 사용하여 구성을 로드하는 경우 *starship.json* 파일의 `starship` 섹션에서 구성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-534">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="6f3f5-535">다음 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-535">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="6f3f5-536">Key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-536">Key</span></span>                   | <span data-ttu-id="6f3f5-537">값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-537">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="6f3f5-538">starship:name</span><span class="sxs-lookup"><span data-stu-id="6f3f5-538">starship:name</span></span>         | <span data-ttu-id="6f3f5-539">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="6f3f5-539">USS Enterprise</span></span>                                    |
| <span data-ttu-id="6f3f5-540">starship:registry</span><span class="sxs-lookup"><span data-stu-id="6f3f5-540">starship:registry</span></span>     | <span data-ttu-id="6f3f5-541">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="6f3f5-541">NCC-1701</span></span>                                          |
| <span data-ttu-id="6f3f5-542">starship:class</span><span class="sxs-lookup"><span data-stu-id="6f3f5-542">starship:class</span></span>        | <span data-ttu-id="6f3f5-543">Constitution</span><span class="sxs-lookup"><span data-stu-id="6f3f5-543">Constitution</span></span>                                      |
| <span data-ttu-id="6f3f5-544">starship:length</span><span class="sxs-lookup"><span data-stu-id="6f3f5-544">starship:length</span></span>       | <span data-ttu-id="6f3f5-545">304.8</span><span class="sxs-lookup"><span data-stu-id="6f3f5-545">304.8</span></span>                                             |
| <span data-ttu-id="6f3f5-546">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="6f3f5-546">starship:commissioned</span></span> | <span data-ttu-id="6f3f5-547">False</span><span class="sxs-lookup"><span data-stu-id="6f3f5-547">False</span></span>                                             |
| <span data-ttu-id="6f3f5-548">trademark</span><span class="sxs-lookup"><span data-stu-id="6f3f5-548">trademark</span></span>             | <span data-ttu-id="6f3f5-549">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="6f3f5-549">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="6f3f5-550">샘플 앱은 `starship` 키를 사용하여 `GetSection`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-550">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="6f3f5-551">`starship` 키-값 쌍은 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-551">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="6f3f5-552">`Bind` 메서드는 `Starship` 클래스의 인스턴스를 전달하는 하위 섹션에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-552">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="6f3f5-553">인스턴스 값을 바인딩한 후 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-553">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="6f3f5-554">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="6f3f5-554">Bind to an object graph</span></span>

<span data-ttu-id="6f3f5-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="6f3f5-556">단순 개체 바인딩과 마찬가지로 공용 읽기/쓰기 속성만 바인딩되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-556">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="6f3f5-557">다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-557">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6f3f5-558">샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-558">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="6f3f5-559">`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-559">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="6f3f5-560">바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-560">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="6f3f5-561">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-561">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="6f3f5-562">`Get<T>`가 `Bind`보다 사용하기 더 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-562">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="6f3f5-563">다음 코드에서는 앞의 예제에 `Get<T>`를 사용하여 바인딩된 인스턴스가 렌더링에 사용되는 속성에 바로 할당될 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-563">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="6f3f5-564">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="6f3f5-564">Bind an array to a class</span></span>

<span data-ttu-id="6f3f5-565">다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다. </span><span class="sxs-lookup"><span data-stu-id="6f3f5-565">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="6f3f5-566"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-566">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="6f3f5-567">숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-567">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="6f3f5-568">바인딩은 규칙에 따라 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-568">Binding is provided by convention.</span></span> <span data-ttu-id="6f3f5-569">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-569">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="6f3f5-570">**메모리 내 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="6f3f5-570">**In-memory array processing**</span></span>

<span data-ttu-id="6f3f5-571">다음 표에 표시된 구성 키 및 값을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-571">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="6f3f5-572">Key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-572">Key</span></span>             | <span data-ttu-id="6f3f5-573">값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-573">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="6f3f5-574">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-574">array:entries:0</span></span> | <span data-ttu-id="6f3f5-575">value0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-575">value0</span></span> |
| <span data-ttu-id="6f3f5-576">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-576">array:entries:1</span></span> | <span data-ttu-id="6f3f5-577">value1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-577">value1</span></span> |
| <span data-ttu-id="6f3f5-578">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-578">array:entries:2</span></span> | <span data-ttu-id="6f3f5-579">value2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-579">value2</span></span> |
| <span data-ttu-id="6f3f5-580">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="6f3f5-580">array:entries:4</span></span> | <span data-ttu-id="6f3f5-581">value4</span><span class="sxs-lookup"><span data-stu-id="6f3f5-581">value4</span></span> |
| <span data-ttu-id="6f3f5-582">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="6f3f5-582">array:entries:5</span></span> | <span data-ttu-id="6f3f5-583">value5</span><span class="sxs-lookup"><span data-stu-id="6f3f5-583">value5</span></span> |

<span data-ttu-id="6f3f5-584">메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-584">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="6f3f5-585">배열은 인덱스 &num;3에 대한 값을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-585">The array skips a value for index &num;3.</span></span> <span data-ttu-id="6f3f5-586">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-586">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="6f3f5-587">샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-587">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6f3f5-588">구성 데이터는 개체에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-588">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="6f3f5-589">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문도 사용할 수 있으며, 이 경우 코드가 더 간결해집니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-589">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="6f3f5-590">바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-590">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="6f3f5-591">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="6f3f5-591">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="6f3f5-592">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-592">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="6f3f5-593">0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-593">0</span></span>                            | <span data-ttu-id="6f3f5-594">value0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-594">value0</span></span>                       |
| <span data-ttu-id="6f3f5-595">1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-595">1</span></span>                            | <span data-ttu-id="6f3f5-596">value1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-596">value1</span></span>                       |
| <span data-ttu-id="6f3f5-597">2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-597">2</span></span>                            | <span data-ttu-id="6f3f5-598">value2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-598">value2</span></span>                       |
| <span data-ttu-id="6f3f5-599">3</span><span class="sxs-lookup"><span data-stu-id="6f3f5-599">3</span></span>                            | <span data-ttu-id="6f3f5-600">value4</span><span class="sxs-lookup"><span data-stu-id="6f3f5-600">value4</span></span>                       |
| <span data-ttu-id="6f3f5-601">4</span><span class="sxs-lookup"><span data-stu-id="6f3f5-601">4</span></span>                            | <span data-ttu-id="6f3f5-602">value5</span><span class="sxs-lookup"><span data-stu-id="6f3f5-602">value5</span></span>                       |

<span data-ttu-id="6f3f5-603">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-603">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="6f3f5-604">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-604">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="6f3f5-605">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-605">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="6f3f5-606">인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-606">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="6f3f5-607">샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-607">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="6f3f5-608">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-608">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="6f3f5-609">`ConfigureAppConfiguration`의 경우</span><span class="sxs-lookup"><span data-stu-id="6f3f5-609">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="6f3f5-610">표에 표시된 키-값 쌍이 구성으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-610">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="6f3f5-611">Key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-611">Key</span></span>             | <span data-ttu-id="6f3f5-612">값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-612">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="6f3f5-613">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="6f3f5-613">array:entries:3</span></span> | <span data-ttu-id="6f3f5-614">value3</span><span class="sxs-lookup"><span data-stu-id="6f3f5-614">value3</span></span> |

<span data-ttu-id="6f3f5-615">JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-615">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="6f3f5-616">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="6f3f5-616">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="6f3f5-617">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-617">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="6f3f5-618">0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-618">0</span></span>                            | <span data-ttu-id="6f3f5-619">value0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-619">value0</span></span>                       |
| <span data-ttu-id="6f3f5-620">1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-620">1</span></span>                            | <span data-ttu-id="6f3f5-621">value1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-621">value1</span></span>                       |
| <span data-ttu-id="6f3f5-622">2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-622">2</span></span>                            | <span data-ttu-id="6f3f5-623">value2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-623">value2</span></span>                       |
| <span data-ttu-id="6f3f5-624">3</span><span class="sxs-lookup"><span data-stu-id="6f3f5-624">3</span></span>                            | <span data-ttu-id="6f3f5-625">value3</span><span class="sxs-lookup"><span data-stu-id="6f3f5-625">value3</span></span>                       |
| <span data-ttu-id="6f3f5-626">4</span><span class="sxs-lookup"><span data-stu-id="6f3f5-626">4</span></span>                            | <span data-ttu-id="6f3f5-627">value4</span><span class="sxs-lookup"><span data-stu-id="6f3f5-627">value4</span></span>                       |
| <span data-ttu-id="6f3f5-628">5</span><span class="sxs-lookup"><span data-stu-id="6f3f5-628">5</span></span>                            | <span data-ttu-id="6f3f5-629">value5</span><span class="sxs-lookup"><span data-stu-id="6f3f5-629">value5</span></span>                       |

<span data-ttu-id="6f3f5-630">**JSON 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="6f3f5-630">**JSON array processing**</span></span>

<span data-ttu-id="6f3f5-631">JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-631">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="6f3f5-632">다음 구성 파일에서 `subsection`은 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-632">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="6f3f5-633">JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-633">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="6f3f5-634">Key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-634">Key</span></span>                     | <span data-ttu-id="6f3f5-635">값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-635">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="6f3f5-636">json_array:key</span><span class="sxs-lookup"><span data-stu-id="6f3f5-636">json_array:key</span></span>          | <span data-ttu-id="6f3f5-637">valueA</span><span class="sxs-lookup"><span data-stu-id="6f3f5-637">valueA</span></span> |
| <span data-ttu-id="6f3f5-638">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-638">json_array:subsection:0</span></span> | <span data-ttu-id="6f3f5-639">valueB</span><span class="sxs-lookup"><span data-stu-id="6f3f5-639">valueB</span></span> |
| <span data-ttu-id="6f3f5-640">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-640">json_array:subsection:1</span></span> | <span data-ttu-id="6f3f5-641">valueC</span><span class="sxs-lookup"><span data-stu-id="6f3f5-641">valueC</span></span> |
| <span data-ttu-id="6f3f5-642">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-642">json_array:subsection:2</span></span> | <span data-ttu-id="6f3f5-643">valueD</span><span class="sxs-lookup"><span data-stu-id="6f3f5-643">valueD</span></span> |

<span data-ttu-id="6f3f5-644">샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-644">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="6f3f5-645">바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-645">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="6f3f5-646">하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-646">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="6f3f5-647">`JsonArrayExample.Subsection` 인덱스</span><span class="sxs-lookup"><span data-stu-id="6f3f5-647">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="6f3f5-648">`JsonArrayExample.Subsection` 값</span><span class="sxs-lookup"><span data-stu-id="6f3f5-648">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="6f3f5-649">0</span><span class="sxs-lookup"><span data-stu-id="6f3f5-649">0</span></span>                                   | <span data-ttu-id="6f3f5-650">valueB</span><span class="sxs-lookup"><span data-stu-id="6f3f5-650">valueB</span></span>                              |
| <span data-ttu-id="6f3f5-651">1</span><span class="sxs-lookup"><span data-stu-id="6f3f5-651">1</span></span>                                   | <span data-ttu-id="6f3f5-652">valueC</span><span class="sxs-lookup"><span data-stu-id="6f3f5-652">valueC</span></span>                              |
| <span data-ttu-id="6f3f5-653">2</span><span class="sxs-lookup"><span data-stu-id="6f3f5-653">2</span></span>                                   | <span data-ttu-id="6f3f5-654">valueD</span><span class="sxs-lookup"><span data-stu-id="6f3f5-654">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="6f3f5-655">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="6f3f5-655">Custom configuration provider</span></span>

<span data-ttu-id="6f3f5-656">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-656">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="6f3f5-657">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-657">The provider has the following characteristics:</span></span>

* <span data-ttu-id="6f3f5-658">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-658">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="6f3f5-659">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-659">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="6f3f5-660">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-660">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="6f3f5-661">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-661">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="6f3f5-662">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-662">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="6f3f5-663">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-663">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f3f5-664">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-664">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="6f3f5-665">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-665">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="6f3f5-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="6f3f5-667"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-667">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="6f3f5-668">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-668">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="6f3f5-669"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-669">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="6f3f5-670">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-670">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="6f3f5-671">[구성 키는 대/소문자를](#keys)구분하지 않으므로 데이터베이스를 초기화하는 데 사용되는 사전은 대/소문자를 구분하지 않는 비교자([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase))를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-671">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="6f3f5-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="6f3f5-673">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-673">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="6f3f5-674">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-674">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="6f3f5-675">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-675">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f3f5-676">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-676">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="6f3f5-677">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-677">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="6f3f5-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="6f3f5-679"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-679">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="6f3f5-680">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-680">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="6f3f5-681"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-681">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="6f3f5-682">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-682">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="6f3f5-683">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-683">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="6f3f5-684">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-684">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="6f3f5-685">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-685">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="6f3f5-686">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-686">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="6f3f5-687">시작하는 동안 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="6f3f5-687">Access configuration during startup</span></span>

<span data-ttu-id="6f3f5-688">`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-688">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f3f5-689">`Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-689">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="6f3f5-690">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-690">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="6f3f5-691">Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="6f3f5-691">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="6f3f5-692">Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-692">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="6f3f5-693">Razor 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-693">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="6f3f5-694">MVC 뷰에서:</span><span class="sxs-lookup"><span data-stu-id="6f3f5-694">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="6f3f5-695">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="6f3f5-695">Add configuration from an external assembly</span></span>

<span data-ttu-id="6f3f5-696"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-696">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="6f3f5-697">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f3f5-697">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f3f5-698">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6f3f5-698">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
