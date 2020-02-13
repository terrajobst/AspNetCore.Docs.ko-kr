---
title: ASP.NET Core의 구성
author: guardrex
description: 구성 API를 사용하여 ASP.NET Core 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d0ef670aa0ac4960318f86ea7888b9eab71f17fd
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171892"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="44e61-103">ASP.NET Core의 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="44e61-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="44e61-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44e61-105">ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="44e61-106">구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="44e61-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="44e61-107">Azure Key Vault</span></span>
* <span data-ttu-id="44e61-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="44e61-108">Azure App Configuration</span></span>
* <span data-ttu-id="44e61-109">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-109">Command-line arguments</span></span>
* <span data-ttu-id="44e61-110">사용자 지정 공급자(설치 또는 생성된)</span><span class="sxs-lookup"><span data-stu-id="44e61-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="44e61-111">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-111">Directory files</span></span>
* <span data-ttu-id="44e61-112">환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-112">Environment variables</span></span>
* <span data-ttu-id="44e61-113">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="44e61-113">In-memory .NET objects</span></span>
* <span data-ttu-id="44e61-114">설정 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-114">Settings files</span></span>

<span data-ttu-id="44e61-115">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 프레임워크에 의해 암시적으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

<span data-ttu-id="44e61-116">샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:</span><span class="sxs-lookup"><span data-stu-id="44e61-116">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="44e61-117">‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-117">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="44e61-118">옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-118">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="44e61-119">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-119">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="44e61-120">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44e61-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="44e61-121">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-121">Host versus app configuration</span></span>

<span data-ttu-id="44e61-122">앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-122">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="44e61-123">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-123">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="44e61-124">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-124">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="44e61-125">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-125">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="44e61-126">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-126">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="44e61-127">기타 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-127">Other configuration</span></span>

<span data-ttu-id="44e61-128">이 항목에서는 *앱 구성*에 관련된 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-128">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="44e61-129">ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-129">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="44e61-130">*launch.json*/*launchSettings.json*은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-130">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="44e61-131"><xref:fundamentals/environments#development> 문서 내</span><span class="sxs-lookup"><span data-stu-id="44e61-131">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="44e61-132">개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체</span><span class="sxs-lookup"><span data-stu-id="44e61-132">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="44e61-133">*web.config*는 다음 항목에 설명된 서버 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-133">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="44e61-134">이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-134">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="44e61-135">기본 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-135">Default configuration</span></span>

<span data-ttu-id="44e61-136">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-136">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="44e61-137">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-137">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="44e61-138">다음은 [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-138">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="44e61-139">[웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 기본 구성에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.2 버전](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-139">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="44e61-140">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-140">Host configuration is provided from:</span></span>
  * <span data-ttu-id="44e61-141">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `DOTNET_`를 접두사로 사용하는 환경 변수(예: `DOTNET_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="44e61-141">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="44e61-142">구성 키-값 쌍이 로드되면 접두사(`DOTNET_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-142">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="44e61-143">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-143">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="44e61-144">웹 호스트 기본 구성이 설정됩니다(`ConfigureWebHostDefaults`).</span><span class="sxs-lookup"><span data-stu-id="44e61-144">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="44e61-145">Kestrel은 웹 서버로 사용되며 앱의 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-145">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="44e61-146">호스트 필터링 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-146">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="44e61-147">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 전달된 헤더 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-147">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="44e61-148">IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-148">Enable IIS integration.</span></span>
* <span data-ttu-id="44e61-149">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-149">App configuration is provided from:</span></span>
  * <span data-ttu-id="44e61-150">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="44e61-150">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="44e61-151">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="44e61-151">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="44e61-152">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-152">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="44e61-153">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-153">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="44e61-154">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-154">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="44e61-155">보안</span><span class="sxs-lookup"><span data-stu-id="44e61-155">Security</span></span>

<span data-ttu-id="44e61-156">중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-156">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="44e61-157">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-157">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="44e61-158">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-158">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="44e61-159">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-159">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="44e61-160">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-160">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="44e61-161"><xref:security/app-secrets> &ndash; 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-161"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="44e61-162">비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-162">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="44e61-163">파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-163">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="44e61-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="44e61-165">자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-165">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="44e61-166">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="44e61-166">Hierarchical configuration data</span></span>

<span data-ttu-id="44e61-167">구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-167">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="44e61-168">다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-168">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="44e61-169">파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-169">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="44e61-170">콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-170">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="44e61-171">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-171">section0:key0</span></span>
* <span data-ttu-id="44e61-172">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-172">section0:key1</span></span>
* <span data-ttu-id="44e61-173">section1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-173">section1:key0</span></span>
* <span data-ttu-id="44e61-174">section1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-174">section1:key1</span></span>

<span data-ttu-id="44e61-175">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="44e61-176">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-176">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="44e61-177">규칙</span><span class="sxs-lookup"><span data-stu-id="44e61-177">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="44e61-178">소스 및 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-178">Sources and providers</span></span>

<span data-ttu-id="44e61-179">앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-179">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="44e61-180">변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-180">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="44e61-181">예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-181">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="44e61-182"><xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="44e61-183"><xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="44e61-184">다음 예제에서는 `_config` 필드가 구성 값에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-184">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="44e61-185">구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-185">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="44e61-186">키</span><span class="sxs-lookup"><span data-stu-id="44e61-186">Keys</span></span>

<span data-ttu-id="44e61-187">구성키는 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-187">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="44e61-188">키는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-188">Keys are case-insensitive.</span></span> <span data-ttu-id="44e61-189">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-189">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="44e61-190">같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-190">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="44e61-191">계층적 키</span><span class="sxs-lookup"><span data-stu-id="44e61-191">Hierarchical keys</span></span>
  * <span data-ttu-id="44e61-192">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-192">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="44e61-193">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-193">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="44e61-194">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-194">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="44e61-195">Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-195">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="44e61-196">비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-196">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="44e61-197"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-197">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="44e61-198">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-198">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="44e61-199">값</span><span class="sxs-lookup"><span data-stu-id="44e61-199">Values</span></span>

<span data-ttu-id="44e61-200">구성 값은 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-200">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="44e61-201">값은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-201">Values are strings.</span></span>
* <span data-ttu-id="44e61-202">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-202">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="44e61-203">공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-203">Providers</span></span>

<span data-ttu-id="44e61-204">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-204">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="44e61-205">공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-205">Provider</span></span> | <span data-ttu-id="44e61-206">다음에서 구성 제공&hellip;</span><span class="sxs-lookup"><span data-stu-id="44e61-206">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="44e61-207">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="44e61-207">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="44e61-208">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="44e61-208">Azure Key Vault</span></span> |
| <span data-ttu-id="44e61-209">[Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="44e61-209">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="44e61-210">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="44e61-210">Azure App Configuration</span></span> |
| [<span data-ttu-id="44e61-211">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="44e61-212">명령줄 매개 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-212">Command-line parameters</span></span> |
| [<span data-ttu-id="44e61-213">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="44e61-214">사용자 지정 소스</span><span class="sxs-lookup"><span data-stu-id="44e61-214">Custom source</span></span> |
| [<span data-ttu-id="44e61-215">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="44e61-216">환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-216">Environment variables</span></span> |
| [<span data-ttu-id="44e61-217">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="44e61-218">파일(INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="44e61-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="44e61-219">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-219">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="44e61-220">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-220">Directory files</span></span> |
| [<span data-ttu-id="44e61-221">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-221">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="44e61-222">메모리 내 컬렉션</span><span class="sxs-lookup"><span data-stu-id="44e61-222">In-memory collections</span></span> |
| <span data-ttu-id="44e61-223">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="44e61-223">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="44e61-224">사용자 프로필 디렉터리의 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-224">File in the user profile directory</span></span> |

<span data-ttu-id="44e61-225">시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-225">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="44e61-226">이 항목의 구성 공급자는 코드에서 정렬하는 순서가 아니라 사전 순으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-226">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="44e61-227">앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-227">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="44e61-228">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-228">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="44e61-229">파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)</span><span class="sxs-lookup"><span data-stu-id="44e61-229">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="44e61-230">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="44e61-230">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="44e61-231">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)</span><span class="sxs-lookup"><span data-stu-id="44e61-231">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="44e61-232">환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-232">Environment variables</span></span>
1. <span data-ttu-id="44e61-233">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-233">Command-line arguments</span></span>

<span data-ttu-id="44e61-234">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-234">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="44e61-235">`CreateDefaultBuilder`로 새 호스트 작성기가 초기화될 때 이전 공급자 시퀀스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-235">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-236">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-236">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="44e61-237">ConfigureHostConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-237">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="44e61-238">호스트 작성기를 구성하려면 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출하고 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-238">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="44e61-239">`ConfigureHostConfiguration`은 나중에 빌드 프로세스에서 사용하기 위해 <xref:Microsoft.Extensions.Hosting.IHostEnvironment>를 초기화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-239">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="44e61-240">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-240">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="44e61-241">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="44e61-241">ConfigureAppConfiguration</span></span>

<span data-ttu-id="44e61-242">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-242">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="44e61-243">명령줄 인수를 사용하여 이전 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="44e61-243">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="44e61-244">명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-244">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="44e61-245">CreateDefaultBuilder에 의해 추가된 공급자 제거</span><span class="sxs-lookup"><span data-stu-id="44e61-245">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="44e61-246">`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-246">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="44e61-247">앱 시작 중 구성 사용</span><span class="sxs-lookup"><span data-stu-id="44e61-247">Consume configuration during app startup</span></span>

<span data-ttu-id="44e61-248">`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-248">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="44e61-249">자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-249">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="44e61-250">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-250">Command-line Configuration Provider</span></span>

<span data-ttu-id="44e61-251"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-251">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-252">명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-252">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-253">`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-253">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="44e61-254">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-254">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-255">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-255">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="44e61-256">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-256">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="44e61-257">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="44e61-257">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="44e61-258">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="44e61-258">Environment variables.</span></span>

<span data-ttu-id="44e61-259">`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-259">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="44e61-260">따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-260">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="44e61-261">`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-261">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="44e61-262">따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-262">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="44e61-263">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-263">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-264">추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-264">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="44e61-265">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-265">**Example**</span></span>

<span data-ttu-id="44e61-266">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-266">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="44e61-267">프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-267">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="44e61-268">`dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).</span><span class="sxs-lookup"><span data-stu-id="44e61-268">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="44e61-269">앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-269">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="44e61-270">`dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-270">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="44e61-271">인수</span><span class="sxs-lookup"><span data-stu-id="44e61-271">Arguments</span></span>

<span data-ttu-id="44e61-272">값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-272">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="44e61-273">등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="44e61-273">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="44e61-274">키 접두사</span><span class="sxs-lookup"><span data-stu-id="44e61-274">Key prefix</span></span>               | <span data-ttu-id="44e61-275">예제</span><span class="sxs-lookup"><span data-stu-id="44e61-275">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="44e61-276">접두사 없음</span><span class="sxs-lookup"><span data-stu-id="44e61-276">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="44e61-277">대시 2개(`--`)</span><span class="sxs-lookup"><span data-stu-id="44e61-277">Two dashes (`--`)</span></span>        | <span data-ttu-id="44e61-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="44e61-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="44e61-279">슬래시(`/`)</span><span class="sxs-lookup"><span data-stu-id="44e61-279">Forward slash (`/`)</span></span>      | <span data-ttu-id="44e61-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="44e61-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="44e61-281">같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-281">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="44e61-282">명령 예:</span><span class="sxs-lookup"><span data-stu-id="44e61-282">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="44e61-283">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="44e61-283">Switch mappings</span></span>

<span data-ttu-id="44e61-284">스위치 매핑은 키 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-284">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="44e61-285"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-285">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="44e61-286">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-286">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="44e61-287">사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-287">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="44e61-288">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-288">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="44e61-289">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="44e61-289">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="44e61-290">스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-290">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="44e61-291">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-291">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="44e61-292">스위치 매핑 사전을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-292">Create a switch mappings dictionary.</span></span> <span data-ttu-id="44e61-293">다음 예에서는 두 개의 스위치 매핑이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-293">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="44e61-294">호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-294">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="44e61-295">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-295">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="44e61-296">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-296">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-297">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-297">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="44e61-298">생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-298">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="44e61-299">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-299">Key</span></span>       | <span data-ttu-id="44e61-300">값</span><span class="sxs-lookup"><span data-stu-id="44e61-300">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="44e61-301">앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-301">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="44e61-302">앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-302">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="44e61-303">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-303">Key</span></span>               | <span data-ttu-id="44e61-304">값</span><span class="sxs-lookup"><span data-stu-id="44e61-304">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="44e61-305">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-305">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="44e61-306"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-306">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-307">환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-307">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="44e61-308">[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="44e61-309">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-309">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="44e61-310">새 호스트 작성기가 [제네릭 호스트](xref:fundamentals/host/generic-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `DOTNET_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-310">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="44e61-311">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-311">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-312">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-312">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="44e61-313">접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-313">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="44e61-314">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-314">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="44e61-315">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="44e61-315">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="44e61-316">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="44e61-316">Command-line arguments.</span></span>

<span data-ttu-id="44e61-317">사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-317">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="44e61-318">이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-318">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="44e61-319">추가 환경 변수에서 앱 구성을 제공하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-319">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="44e61-320">지정된 접두사를 포함하는 환경 변수에서 다른 공급자의 값을 재정의할 수 있도록 하려면 `AddEnvironmentVariables`를 마지막으로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-320">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="44e61-321">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-321">**Example**</span></span>

<span data-ttu-id="44e61-322">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-322">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="44e61-323">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-323">Run the sample app.</span></span> <span data-ttu-id="44e61-324">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-324">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="44e61-325">환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-325">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="44e61-326">이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-326">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="44e61-327">앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-327">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="44e61-328">샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-328">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="44e61-329">앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-329">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="44e61-330">접두사</span><span class="sxs-lookup"><span data-stu-id="44e61-330">Prefixes</span></span>

<span data-ttu-id="44e61-331">`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-331">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="44e61-332">예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-332">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="44e61-333">구성 키-값 쌍이 생성되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-333">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="44e61-334">호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-334">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="44e61-335">환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-335">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-336">**연결 문자열 접두사**</span><span class="sxs-lookup"><span data-stu-id="44e61-336">**Connection string prefixes**</span></span>

<span data-ttu-id="44e61-337">구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-337">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="44e61-338">즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-338">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="44e61-339">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="44e61-339">Connection string prefix</span></span> | <span data-ttu-id="44e61-340">공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-340">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="44e61-341">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-341">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="44e61-342">MySQL</span><span class="sxs-lookup"><span data-stu-id="44e61-342">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="44e61-343">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="44e61-343">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="44e61-344">SQL Server</span><span class="sxs-lookup"><span data-stu-id="44e61-344">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="44e61-345">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-345">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="44e61-346">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-346">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="44e61-347">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-347">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="44e61-348">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="44e61-348">Environment variable key</span></span> | <span data-ttu-id="44e61-349">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="44e61-349">Converted configuration key</span></span> | <span data-ttu-id="44e61-350">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="44e61-350">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-351">구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-351">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-352">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="44e61-352">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="44e61-353">값: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="44e61-353">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-354">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="44e61-354">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="44e61-355">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="44e61-355">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-356">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="44e61-356">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="44e61-357">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="44e61-357">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="44e61-358">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-358">**Example**</span></span>

<span data-ttu-id="44e61-359">서버에 사용자 지정 연결 문자열 환경 변수가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-359">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="44e61-360">이름 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="44e61-360">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="44e61-361">값 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="44e61-361">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="44e61-362">`IConfiguration`이 삽입되고 `_config`라는 필드에 할당된 경우 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-362">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="44e61-363">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-363">File Configuration Provider</span></span>

<span data-ttu-id="44e61-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="44e61-365">다음 구성 공급자는 특정 파일 형식 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-365">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="44e61-366">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-366">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="44e61-367">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-367">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="44e61-368">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-368">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="44e61-369">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-369">INI Configuration Provider</span></span>

<span data-ttu-id="44e61-370"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-370">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-371">INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-371">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-372">콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-372">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="44e61-373">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-373">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-374">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-374">Whether the file is optional.</span></span>
* <span data-ttu-id="44e61-375">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-375">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="44e61-376">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="44e61-376">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="44e61-377">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-377">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="44e61-378">INI 구성 파일의 일반적인 예:</span><span class="sxs-lookup"><span data-stu-id="44e61-378">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="44e61-379">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-379">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-380">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-380">section0:key0</span></span>
* <span data-ttu-id="44e61-381">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-381">section0:key1</span></span>
* <span data-ttu-id="44e61-382">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="44e61-382">section1:subsection:key</span></span>
* <span data-ttu-id="44e61-383">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="44e61-383">section2:subsection0:key</span></span>
* <span data-ttu-id="44e61-384">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="44e61-384">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="44e61-385">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-385">JSON Configuration Provider</span></span>

<span data-ttu-id="44e61-386"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-386">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="44e61-387">JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-387">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-388">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-388">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-389">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-389">Whether the file is optional.</span></span>
* <span data-ttu-id="44e61-390">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-390">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="44e61-391">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="44e61-391">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="44e61-392">`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-392">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-393">이 메서드는 호출되면 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-393">The method is called to load configuration from:</span></span>

* <span data-ttu-id="44e61-394">*appsettings.json* &ndash; 이 파일을 먼저 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-394">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="44e61-395">파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-395">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="44e61-396">*appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-396">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="44e61-397">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-397">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-398">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-398">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="44e61-399">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="44e61-399">Environment variables.</span></span>
* <span data-ttu-id="44e61-400">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="44e61-400">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="44e61-401">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="44e61-401">Command-line arguments.</span></span>

<span data-ttu-id="44e61-402">JSON 구성 공급자를 먼저 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-402">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="44e61-403">따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-403">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="44e61-404">*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-404">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="44e61-405">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-405">**Example**</span></span>

<span data-ttu-id="44e61-406">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-406">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="44e61-407">`AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-407">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="44e61-408">`AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-408">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="44e61-409">샘플 앱의 *appsettings.Development.json*의 경우 다음 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-409">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="44e61-410">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-410">Run the sample app.</span></span> <span data-ttu-id="44e61-411">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-411">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="44e61-412">출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-412">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="44e61-413">개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-413">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="44e61-414">프로덕션 환경에서 샘플 앱을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-414">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="44e61-415">*Properties/launchSettings.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-415">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="44e61-416">`ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-416">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="44e61-417">파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-417">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="44e61-418">*appsettings.Development.json*의 설정에서 더 이상 *appsettings.json*의 설정을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-418">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="44e61-419">`Logging:LogLevel:Default` 키의 로그 수준은 `Information`입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-419">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="44e61-420">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-420">XML Configuration Provider</span></span>

<span data-ttu-id="44e61-421"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-421">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-422">XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-422">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-423">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-423">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-424">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-424">Whether the file is optional.</span></span>
* <span data-ttu-id="44e61-425">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-425">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="44e61-426">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="44e61-426">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="44e61-427">구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-427">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="44e61-428">파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-428">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="44e61-429">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-429">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="44e61-430">XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-430">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="44e61-431">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-431">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-432">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-432">section0:key0</span></span>
* <span data-ttu-id="44e61-433">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-433">section0:key1</span></span>
* <span data-ttu-id="44e61-434">section1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-434">section1:key0</span></span>
* <span data-ttu-id="44e61-435">section1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-435">section1:key1</span></span>

<span data-ttu-id="44e61-436">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-436">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="44e61-437">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-437">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-438">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-438">section:section0:key:key0</span></span>
* <span data-ttu-id="44e61-439">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-439">section:section0:key:key1</span></span>
* <span data-ttu-id="44e61-440">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-440">section:section1:key:key0</span></span>
* <span data-ttu-id="44e61-441">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-441">section:section1:key:key1</span></span>

<span data-ttu-id="44e61-442">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-442">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="44e61-443">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-443">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-444">key:attribute</span><span class="sxs-lookup"><span data-stu-id="44e61-444">key:attribute</span></span>
* <span data-ttu-id="44e61-445">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="44e61-445">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="44e61-446">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-446">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="44e61-447"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-447">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="44e61-448">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="44e61-448">The key is the file name.</span></span> <span data-ttu-id="44e61-449">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-449">The value contains the file's contents.</span></span> <span data-ttu-id="44e61-450">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-450">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="44e61-451">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-451">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="44e61-452">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-452">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="44e61-453">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-453">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-454">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="44e61-454">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="44e61-455">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="44e61-455">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="44e61-456">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-456">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="44e61-457">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-457">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="44e61-458">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-458">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="44e61-459">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-459">Memory Configuration Provider</span></span>

<span data-ttu-id="44e61-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="44e61-461">메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-462">`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="44e61-463">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-463">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="44e61-464">다음 예제에서는 구성 사전이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-464">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="44e61-465">사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-465">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="44e61-466">GetValue</span><span class="sxs-lookup"><span data-stu-id="44e61-466">GetValue</span></span>

<span data-ttu-id="44e61-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 하나의 값을 추출하고 해당 값을 지정된 비컬렉션 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="44e61-468">오버로드는 기본값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-468">An overload accepts a default value.</span></span>

<span data-ttu-id="44e61-469">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="44e61-469">The following example:</span></span>

* <span data-ttu-id="44e61-470">`NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-470">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="44e61-471">구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-471">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="44e61-472">값을 `int` 형식으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-472">Types the value as an `int`.</span></span>
* <span data-ttu-id="44e61-473">페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-473">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="44e61-474">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="44e61-474">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="44e61-475">이어지는 예제에서는 다음 JSON 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-475">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="44e61-476">네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-476">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="44e61-477">파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-477">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="44e61-478">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-478">section0:key0</span></span>
* <span data-ttu-id="44e61-479">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-479">section0:key1</span></span>
* <span data-ttu-id="44e61-480">section1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-480">section1:key0</span></span>
* <span data-ttu-id="44e61-481">section1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-481">section1:key1</span></span>
* <span data-ttu-id="44e61-482">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-482">section2:subsection0:key0</span></span>
* <span data-ttu-id="44e61-483">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-483">section2:subsection0:key1</span></span>
* <span data-ttu-id="44e61-484">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-484">section2:subsection1:key0</span></span>
* <span data-ttu-id="44e61-485">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-485">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="44e61-486">GetSection</span><span class="sxs-lookup"><span data-stu-id="44e61-486">GetSection</span></span>

<span data-ttu-id="44e61-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="44e61-488">`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="44e61-489">`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="44e61-490">마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="44e61-491">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="44e61-492">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="44e61-493">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="44e61-494">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="44e61-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="44e61-495">GetChildren</span></span>

<span data-ttu-id="44e61-496">`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="44e61-497">있음</span><span class="sxs-lookup"><span data-stu-id="44e61-497">Exists</span></span>

<span data-ttu-id="44e61-498">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="44e61-499">예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="44e61-500">클래스에 바인딩</span><span class="sxs-lookup"><span data-stu-id="44e61-500">Bind to a class</span></span>

<span data-ttu-id="44e61-501">‘옵션 패턴’을 사용하여 관련 설정 그룹을 나타내는 클래스에 구성을 바인딩할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="44e61-502">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="44e61-503">구성 값이 문자열로 반환되지만, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>를 호출하면 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="44e61-504">바인더는 제공된 형식의 모든 공용 읽기/쓰기 속성에 값을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-504">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="44e61-505">필드가 바인딩되지 **않았습니다**.</span><span class="sxs-lookup"><span data-stu-id="44e61-505">Fields are **not** bound.</span></span>

<span data-ttu-id="44e61-506">샘플 앱은 `Starship` 모델(*Models/Starship.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-506">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="44e61-507">샘플 앱이 JSON 구성 공급자를 사용하여 구성을 로드하는 경우 *starship.json* 파일의 `starship` 섹션에서 구성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-507">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

<span data-ttu-id="44e61-508">다음 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-508">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="44e61-509">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-509">Key</span></span>                   | <span data-ttu-id="44e61-510">값</span><span class="sxs-lookup"><span data-stu-id="44e61-510">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="44e61-511">starship:name</span><span class="sxs-lookup"><span data-stu-id="44e61-511">starship:name</span></span>         | <span data-ttu-id="44e61-512">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="44e61-512">USS Enterprise</span></span>                                    |
| <span data-ttu-id="44e61-513">starship:registry</span><span class="sxs-lookup"><span data-stu-id="44e61-513">starship:registry</span></span>     | <span data-ttu-id="44e61-514">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="44e61-514">NCC-1701</span></span>                                          |
| <span data-ttu-id="44e61-515">starship:class</span><span class="sxs-lookup"><span data-stu-id="44e61-515">starship:class</span></span>        | <span data-ttu-id="44e61-516">Constitution</span><span class="sxs-lookup"><span data-stu-id="44e61-516">Constitution</span></span>                                      |
| <span data-ttu-id="44e61-517">starship:length</span><span class="sxs-lookup"><span data-stu-id="44e61-517">starship:length</span></span>       | <span data-ttu-id="44e61-518">304.8</span><span class="sxs-lookup"><span data-stu-id="44e61-518">304.8</span></span>                                             |
| <span data-ttu-id="44e61-519">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="44e61-519">starship:commissioned</span></span> | <span data-ttu-id="44e61-520">False</span><span class="sxs-lookup"><span data-stu-id="44e61-520">False</span></span>                                             |
| <span data-ttu-id="44e61-521">trademark</span><span class="sxs-lookup"><span data-stu-id="44e61-521">trademark</span></span>             | <span data-ttu-id="44e61-522">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="44e61-522">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="44e61-523">샘플 앱은 `starship` 키를 사용하여 `GetSection`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-523">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="44e61-524">`starship` 키-값 쌍은 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-524">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="44e61-525">`Bind` 메서드는 `Starship` 클래스의 인스턴스를 전달하는 하위 섹션에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-525">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="44e61-526">인스턴스 값을 바인딩한 후 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-526">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="44e61-527">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="44e61-527">Bind to an object graph</span></span>

<span data-ttu-id="44e61-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="44e61-529">단순 개체 바인딩과 마찬가지로 공용 읽기/쓰기 속성만 바인딩되었습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-529">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="44e61-530">다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="44e61-531">샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="44e61-532">`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="44e61-533">바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="44e61-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="44e61-535">`Get<T>`가 `Bind`보다 사용하기 더 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="44e61-536">다음 코드에서는 앞의 예제에 `Get<T>`를 사용하여 바인딩된 인스턴스가 렌더링에 사용되는 속성에 바로 할당될 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="44e61-537">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="44e61-537">Bind an array to a class</span></span>

<span data-ttu-id="44e61-538">다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-538">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="44e61-539"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-539">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="44e61-540">숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-540">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="44e61-541">바인딩은 규칙에 따라 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-541">Binding is provided by convention.</span></span> <span data-ttu-id="44e61-542">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-542">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="44e61-543">**메모리 내 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="44e61-543">**In-memory array processing**</span></span>

<span data-ttu-id="44e61-544">다음 표에 표시된 구성 키 및 값을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-544">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="44e61-545">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-545">Key</span></span>             | <span data-ttu-id="44e61-546">값</span><span class="sxs-lookup"><span data-stu-id="44e61-546">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="44e61-547">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="44e61-547">array:entries:0</span></span> | <span data-ttu-id="44e61-548">value0</span><span class="sxs-lookup"><span data-stu-id="44e61-548">value0</span></span> |
| <span data-ttu-id="44e61-549">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="44e61-549">array:entries:1</span></span> | <span data-ttu-id="44e61-550">value1</span><span class="sxs-lookup"><span data-stu-id="44e61-550">value1</span></span> |
| <span data-ttu-id="44e61-551">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="44e61-551">array:entries:2</span></span> | <span data-ttu-id="44e61-552">value2</span><span class="sxs-lookup"><span data-stu-id="44e61-552">value2</span></span> |
| <span data-ttu-id="44e61-553">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="44e61-553">array:entries:4</span></span> | <span data-ttu-id="44e61-554">value4</span><span class="sxs-lookup"><span data-stu-id="44e61-554">value4</span></span> |
| <span data-ttu-id="44e61-555">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="44e61-555">array:entries:5</span></span> | <span data-ttu-id="44e61-556">value5</span><span class="sxs-lookup"><span data-stu-id="44e61-556">value5</span></span> |

<span data-ttu-id="44e61-557">메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-557">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="44e61-558">배열은 인덱스 &num;3에 대한 값을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-558">The array skips a value for index &num;3.</span></span> <span data-ttu-id="44e61-559">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-559">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="44e61-560">샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-560">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="44e61-561">구성 데이터는 개체에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-561">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="44e61-562">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문도 사용할 수 있으며, 이 경우 코드가 더 간결해집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-562">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="44e61-563">바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-563">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="44e61-564">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="44e61-564">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="44e61-565">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="44e61-565">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="44e61-566">0</span><span class="sxs-lookup"><span data-stu-id="44e61-566">0</span></span>                            | <span data-ttu-id="44e61-567">value0</span><span class="sxs-lookup"><span data-stu-id="44e61-567">value0</span></span>                       |
| <span data-ttu-id="44e61-568">1</span><span class="sxs-lookup"><span data-stu-id="44e61-568">1</span></span>                            | <span data-ttu-id="44e61-569">value1</span><span class="sxs-lookup"><span data-stu-id="44e61-569">value1</span></span>                       |
| <span data-ttu-id="44e61-570">2</span><span class="sxs-lookup"><span data-stu-id="44e61-570">2</span></span>                            | <span data-ttu-id="44e61-571">value2</span><span class="sxs-lookup"><span data-stu-id="44e61-571">value2</span></span>                       |
| <span data-ttu-id="44e61-572">3</span><span class="sxs-lookup"><span data-stu-id="44e61-572">3</span></span>                            | <span data-ttu-id="44e61-573">value4</span><span class="sxs-lookup"><span data-stu-id="44e61-573">value4</span></span>                       |
| <span data-ttu-id="44e61-574">4</span><span class="sxs-lookup"><span data-stu-id="44e61-574">4</span></span>                            | <span data-ttu-id="44e61-575">value5</span><span class="sxs-lookup"><span data-stu-id="44e61-575">value5</span></span>                       |

<span data-ttu-id="44e61-576">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-576">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="44e61-577">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-577">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="44e61-578">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-578">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="44e61-579">인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-579">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="44e61-580">샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-580">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="44e61-581">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="44e61-581">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="44e61-582">`ConfigureAppConfiguration`의 경우</span><span class="sxs-lookup"><span data-stu-id="44e61-582">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="44e61-583">표에 표시된 키-값 쌍이 구성으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-583">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="44e61-584">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-584">Key</span></span>             | <span data-ttu-id="44e61-585">값</span><span class="sxs-lookup"><span data-stu-id="44e61-585">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="44e61-586">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="44e61-586">array:entries:3</span></span> | <span data-ttu-id="44e61-587">value3</span><span class="sxs-lookup"><span data-stu-id="44e61-587">value3</span></span> |

<span data-ttu-id="44e61-588">JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-588">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="44e61-589">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="44e61-589">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="44e61-590">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="44e61-590">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="44e61-591">0</span><span class="sxs-lookup"><span data-stu-id="44e61-591">0</span></span>                            | <span data-ttu-id="44e61-592">value0</span><span class="sxs-lookup"><span data-stu-id="44e61-592">value0</span></span>                       |
| <span data-ttu-id="44e61-593">1</span><span class="sxs-lookup"><span data-stu-id="44e61-593">1</span></span>                            | <span data-ttu-id="44e61-594">value1</span><span class="sxs-lookup"><span data-stu-id="44e61-594">value1</span></span>                       |
| <span data-ttu-id="44e61-595">2</span><span class="sxs-lookup"><span data-stu-id="44e61-595">2</span></span>                            | <span data-ttu-id="44e61-596">value2</span><span class="sxs-lookup"><span data-stu-id="44e61-596">value2</span></span>                       |
| <span data-ttu-id="44e61-597">3</span><span class="sxs-lookup"><span data-stu-id="44e61-597">3</span></span>                            | <span data-ttu-id="44e61-598">value3</span><span class="sxs-lookup"><span data-stu-id="44e61-598">value3</span></span>                       |
| <span data-ttu-id="44e61-599">4</span><span class="sxs-lookup"><span data-stu-id="44e61-599">4</span></span>                            | <span data-ttu-id="44e61-600">value4</span><span class="sxs-lookup"><span data-stu-id="44e61-600">value4</span></span>                       |
| <span data-ttu-id="44e61-601">5</span><span class="sxs-lookup"><span data-stu-id="44e61-601">5</span></span>                            | <span data-ttu-id="44e61-602">value5</span><span class="sxs-lookup"><span data-stu-id="44e61-602">value5</span></span>                       |

<span data-ttu-id="44e61-603">**JSON 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="44e61-603">**JSON array processing**</span></span>

<span data-ttu-id="44e61-604">JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-604">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="44e61-605">다음 구성 파일에서 `subsection`은 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-605">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="44e61-606">JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-606">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="44e61-607">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-607">Key</span></span>                     | <span data-ttu-id="44e61-608">값</span><span class="sxs-lookup"><span data-stu-id="44e61-608">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="44e61-609">json_array:key</span><span class="sxs-lookup"><span data-stu-id="44e61-609">json_array:key</span></span>          | <span data-ttu-id="44e61-610">valueA</span><span class="sxs-lookup"><span data-stu-id="44e61-610">valueA</span></span> |
| <span data-ttu-id="44e61-611">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="44e61-611">json_array:subsection:0</span></span> | <span data-ttu-id="44e61-612">valueB</span><span class="sxs-lookup"><span data-stu-id="44e61-612">valueB</span></span> |
| <span data-ttu-id="44e61-613">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="44e61-613">json_array:subsection:1</span></span> | <span data-ttu-id="44e61-614">valueC</span><span class="sxs-lookup"><span data-stu-id="44e61-614">valueC</span></span> |
| <span data-ttu-id="44e61-615">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="44e61-615">json_array:subsection:2</span></span> | <span data-ttu-id="44e61-616">valueD</span><span class="sxs-lookup"><span data-stu-id="44e61-616">valueD</span></span> |

<span data-ttu-id="44e61-617">샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-617">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="44e61-618">바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-618">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="44e61-619">하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-619">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="44e61-620">`JsonArrayExample.Subsection` 인덱스</span><span class="sxs-lookup"><span data-stu-id="44e61-620">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="44e61-621">`JsonArrayExample.Subsection` 값</span><span class="sxs-lookup"><span data-stu-id="44e61-621">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="44e61-622">0</span><span class="sxs-lookup"><span data-stu-id="44e61-622">0</span></span>                                   | <span data-ttu-id="44e61-623">valueB</span><span class="sxs-lookup"><span data-stu-id="44e61-623">valueB</span></span>                              |
| <span data-ttu-id="44e61-624">1</span><span class="sxs-lookup"><span data-stu-id="44e61-624">1</span></span>                                   | <span data-ttu-id="44e61-625">valueC</span><span class="sxs-lookup"><span data-stu-id="44e61-625">valueC</span></span>                              |
| <span data-ttu-id="44e61-626">2</span><span class="sxs-lookup"><span data-stu-id="44e61-626">2</span></span>                                   | <span data-ttu-id="44e61-627">valueD</span><span class="sxs-lookup"><span data-stu-id="44e61-627">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="44e61-628">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-628">Custom configuration provider</span></span>

<span data-ttu-id="44e61-629">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-629">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="44e61-630">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-630">The provider has the following characteristics:</span></span>

* <span data-ttu-id="44e61-631">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-631">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="44e61-632">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-632">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="44e61-633">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-633">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="44e61-634">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-634">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="44e61-635">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-635">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="44e61-636">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-636">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="44e61-637">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-637">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="44e61-638">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-638">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="44e61-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="44e61-640"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-640">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="44e61-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="44e61-642"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-642">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="44e61-643">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-643">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="44e61-644">[구성 키는 대/소문자를](#keys)구분하지 않으므로 데이터베이스를 초기화하는 데 사용되는 사전은 대/소문자를 구분하지 않는 비교자([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase))를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-644">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="44e61-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="44e61-646">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-646">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="44e61-647">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-647">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="44e61-648">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-648">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="44e61-649">시작하는 동안 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="44e61-649">Access configuration during startup</span></span>

<span data-ttu-id="44e61-650">`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-650">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="44e61-651">`Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-651">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="44e61-652">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-652">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="44e61-653">Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="44e61-653">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="44e61-654">Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-654">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="44e61-655">Razor 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="44e61-655">In a Razor Pages page:</span></span>

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

<span data-ttu-id="44e61-656">MVC 뷰에서:</span><span class="sxs-lookup"><span data-stu-id="44e61-656">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="44e61-657">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="44e61-657">Add configuration from an external assembly</span></span>

<span data-ttu-id="44e61-658"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-658">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="44e61-659">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-659">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44e61-660">추가 자료</span><span class="sxs-lookup"><span data-stu-id="44e61-660">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="44e61-661">ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-661">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="44e61-662">구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-662">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="44e61-663">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="44e61-663">Azure Key Vault</span></span>
* <span data-ttu-id="44e61-664">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="44e61-664">Azure App Configuration</span></span>
* <span data-ttu-id="44e61-665">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-665">Command-line arguments</span></span>
* <span data-ttu-id="44e61-666">사용자 지정 공급자(설치 또는 생성된)</span><span class="sxs-lookup"><span data-stu-id="44e61-666">Custom providers (installed or created)</span></span>
* <span data-ttu-id="44e61-667">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-667">Directory files</span></span>
* <span data-ttu-id="44e61-668">환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-668">Environment variables</span></span>
* <span data-ttu-id="44e61-669">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="44e61-669">In-memory .NET objects</span></span>
* <span data-ttu-id="44e61-670">설정 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-670">Settings files</span></span>

<span data-ttu-id="44e61-671">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-671">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="44e61-672">샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:</span><span class="sxs-lookup"><span data-stu-id="44e61-672">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="44e61-673">‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-673">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="44e61-674">옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-674">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="44e61-675">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-675">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="44e61-676">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44e61-676">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="44e61-677">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-677">Host versus app configuration</span></span>

<span data-ttu-id="44e61-678">앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-678">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="44e61-679">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-679">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="44e61-680">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-680">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="44e61-681">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-681">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="44e61-682">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-682">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="44e61-683">기타 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-683">Other configuration</span></span>

<span data-ttu-id="44e61-684">이 항목에서는 *앱 구성*에 관련된 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-684">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="44e61-685">ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-685">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="44e61-686">*launch.json*/*launchSettings.json*은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-686">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="44e61-687"><xref:fundamentals/environments#development> 문서 내</span><span class="sxs-lookup"><span data-stu-id="44e61-687">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="44e61-688">개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체</span><span class="sxs-lookup"><span data-stu-id="44e61-688">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="44e61-689">*web.config*는 다음 항목에 설명된 서버 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-689">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="44e61-690">이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-690">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="44e61-691">기본 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-691">Default configuration</span></span>

<span data-ttu-id="44e61-692">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-692">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="44e61-693">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-693">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="44e61-694">[웹호스트](xref:fundamentals/host/web-host)를 사용하는 앱에는 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-694">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="44e61-695">[제네릭 호스트](xref:fundamentals/host/generic-host) 사용 시 기본 구성에 대한 자세한 내용은 [이 토픽의 최신 버전](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-695">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="44e61-696">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-696">Host configuration is provided from:</span></span>
  * <span data-ttu-id="44e61-697">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `ASPNETCORE_`를 접두사로 사용하는 환경 변수(예: `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="44e61-697">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="44e61-698">구성 키-값 쌍이 로드되면 접두사(`ASPNETCORE_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-698">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="44e61-699">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-699">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="44e61-700">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-700">App configuration is provided from:</span></span>
  * <span data-ttu-id="44e61-701">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="44e61-701">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="44e61-702">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="44e61-702">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="44e61-703">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-703">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="44e61-704">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-704">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="44e61-705">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-705">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="44e61-706">보안</span><span class="sxs-lookup"><span data-stu-id="44e61-706">Security</span></span>

<span data-ttu-id="44e61-707">중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-707">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="44e61-708">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-708">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="44e61-709">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-709">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="44e61-710">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-710">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="44e61-711">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-711">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="44e61-712"><xref:security/app-secrets> &ndash; 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-712"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="44e61-713">비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-713">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="44e61-714">파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-714">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="44e61-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="44e61-716">자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-716">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="44e61-717">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="44e61-717">Hierarchical configuration data</span></span>

<span data-ttu-id="44e61-718">구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-718">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="44e61-719">다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-719">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="44e61-720">파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-720">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="44e61-721">콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-721">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="44e61-722">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-722">section0:key0</span></span>
* <span data-ttu-id="44e61-723">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-723">section0:key1</span></span>
* <span data-ttu-id="44e61-724">section1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-724">section1:key0</span></span>
* <span data-ttu-id="44e61-725">section1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-725">section1:key1</span></span>

<span data-ttu-id="44e61-726">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="44e61-727">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-727">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="44e61-728">규칙</span><span class="sxs-lookup"><span data-stu-id="44e61-728">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="44e61-729">소스 및 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-729">Sources and providers</span></span>

<span data-ttu-id="44e61-730">앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-730">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="44e61-731">변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-731">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="44e61-732">예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-732">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="44e61-733"><xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="44e61-734"><xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="44e61-735">다음 예제에서는 `_config` 필드가 구성 값에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-735">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="44e61-736">구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-736">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="44e61-737">키</span><span class="sxs-lookup"><span data-stu-id="44e61-737">Keys</span></span>

<span data-ttu-id="44e61-738">구성키는 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-738">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="44e61-739">키는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-739">Keys are case-insensitive.</span></span> <span data-ttu-id="44e61-740">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-740">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="44e61-741">같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-741">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="44e61-742">계층적 키</span><span class="sxs-lookup"><span data-stu-id="44e61-742">Hierarchical keys</span></span>
  * <span data-ttu-id="44e61-743">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-743">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="44e61-744">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-744">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="44e61-745">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-745">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="44e61-746">Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-746">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="44e61-747">비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-747">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="44e61-748"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-748">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="44e61-749">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-749">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="44e61-750">값</span><span class="sxs-lookup"><span data-stu-id="44e61-750">Values</span></span>

<span data-ttu-id="44e61-751">구성 값은 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-751">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="44e61-752">값은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-752">Values are strings.</span></span>
* <span data-ttu-id="44e61-753">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-753">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="44e61-754">공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-754">Providers</span></span>

<span data-ttu-id="44e61-755">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-755">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="44e61-756">공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-756">Provider</span></span> | <span data-ttu-id="44e61-757">다음에서 구성 제공&hellip;</span><span class="sxs-lookup"><span data-stu-id="44e61-757">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="44e61-758">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="44e61-758">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="44e61-759">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="44e61-759">Azure Key Vault</span></span> |
| <span data-ttu-id="44e61-760">[Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="44e61-760">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="44e61-761">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="44e61-761">Azure App Configuration</span></span> |
| [<span data-ttu-id="44e61-762">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-762">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="44e61-763">명령줄 매개 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-763">Command-line parameters</span></span> |
| [<span data-ttu-id="44e61-764">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-764">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="44e61-765">사용자 지정 소스</span><span class="sxs-lookup"><span data-stu-id="44e61-765">Custom source</span></span> |
| [<span data-ttu-id="44e61-766">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-766">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="44e61-767">환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-767">Environment variables</span></span> |
| [<span data-ttu-id="44e61-768">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-768">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="44e61-769">파일(INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="44e61-769">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="44e61-770">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-770">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="44e61-771">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-771">Directory files</span></span> |
| [<span data-ttu-id="44e61-772">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-772">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="44e61-773">메모리 내 컬렉션</span><span class="sxs-lookup"><span data-stu-id="44e61-773">In-memory collections</span></span> |
| <span data-ttu-id="44e61-774">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="44e61-774">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="44e61-775">사용자 프로필 디렉터리의 파일</span><span class="sxs-lookup"><span data-stu-id="44e61-775">File in the user profile directory</span></span> |

<span data-ttu-id="44e61-776">시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-776">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="44e61-777">이 항목의 구성 공급자는 코드에서 정렬하는 순서가 아니라 사전 순으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-777">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="44e61-778">앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-778">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="44e61-779">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-779">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="44e61-780">파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)</span><span class="sxs-lookup"><span data-stu-id="44e61-780">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="44e61-781">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="44e61-781">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="44e61-782">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)</span><span class="sxs-lookup"><span data-stu-id="44e61-782">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="44e61-783">환경 변수</span><span class="sxs-lookup"><span data-stu-id="44e61-783">Environment variables</span></span>
1. <span data-ttu-id="44e61-784">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="44e61-784">Command-line arguments</span></span>

<span data-ttu-id="44e61-785">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-785">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="44e61-786">`CreateDefaultBuilder`로 새 호스트 작성기가 초기화될 때 이전 공급자 시퀀스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-786">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-787">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-787">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="44e61-788">UseConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-788">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="44e61-789">호스트 작성기를 구성하려면 구성과 함께 호스트 작성기에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-789">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="44e61-790">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="44e61-790">ConfigureAppConfiguration</span></span>

<span data-ttu-id="44e61-791">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-791">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="44e61-792">명령줄 인수를 사용하여 이전 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="44e61-792">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="44e61-793">명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-793">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="44e61-794">CreateDefaultBuilder에 의해 추가된 공급자 제거</span><span class="sxs-lookup"><span data-stu-id="44e61-794">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="44e61-795">`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-795">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="44e61-796">앱 시작 중 구성 사용</span><span class="sxs-lookup"><span data-stu-id="44e61-796">Consume configuration during app startup</span></span>

<span data-ttu-id="44e61-797">`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-797">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="44e61-798">자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-798">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="44e61-799">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-799">Command-line Configuration Provider</span></span>

<span data-ttu-id="44e61-800"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-800">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-801">명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-801">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-802">`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-802">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="44e61-803">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-803">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-804">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-804">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="44e61-805">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-805">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="44e61-806">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="44e61-806">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="44e61-807">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="44e61-807">Environment variables.</span></span>

<span data-ttu-id="44e61-808">`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-808">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="44e61-809">따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-809">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="44e61-810">`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-810">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="44e61-811">따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-811">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="44e61-812">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-812">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-813">추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-813">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="44e61-814">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-814">**Example**</span></span>

<span data-ttu-id="44e61-815">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-815">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="44e61-816">프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-816">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="44e61-817">`dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).</span><span class="sxs-lookup"><span data-stu-id="44e61-817">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="44e61-818">앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-818">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="44e61-819">`dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-819">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="44e61-820">인수</span><span class="sxs-lookup"><span data-stu-id="44e61-820">Arguments</span></span>

<span data-ttu-id="44e61-821">값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-821">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="44e61-822">등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="44e61-822">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="44e61-823">키 접두사</span><span class="sxs-lookup"><span data-stu-id="44e61-823">Key prefix</span></span>               | <span data-ttu-id="44e61-824">예제</span><span class="sxs-lookup"><span data-stu-id="44e61-824">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="44e61-825">접두사 없음</span><span class="sxs-lookup"><span data-stu-id="44e61-825">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="44e61-826">대시 2개(`--`)</span><span class="sxs-lookup"><span data-stu-id="44e61-826">Two dashes (`--`)</span></span>        | <span data-ttu-id="44e61-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="44e61-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="44e61-828">슬래시(`/`)</span><span class="sxs-lookup"><span data-stu-id="44e61-828">Forward slash (`/`)</span></span>      | <span data-ttu-id="44e61-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="44e61-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="44e61-830">같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-830">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="44e61-831">명령 예:</span><span class="sxs-lookup"><span data-stu-id="44e61-831">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="44e61-832">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="44e61-832">Switch mappings</span></span>

<span data-ttu-id="44e61-833">스위치 매핑은 키 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-833">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="44e61-834"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-834">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="44e61-835">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-835">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="44e61-836">사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-836">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="44e61-837">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-837">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="44e61-838">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="44e61-838">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="44e61-839">스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-839">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="44e61-840">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-840">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="44e61-841">스위치 매핑 사전을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-841">Create a switch mappings dictionary.</span></span> <span data-ttu-id="44e61-842">다음 예에서는 두 개의 스위치 매핑이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-842">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="44e61-843">호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-843">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="44e61-844">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-844">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="44e61-845">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-845">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-846">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-846">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="44e61-847">생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-847">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="44e61-848">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-848">Key</span></span>       | <span data-ttu-id="44e61-849">값</span><span class="sxs-lookup"><span data-stu-id="44e61-849">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="44e61-850">앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-850">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="44e61-851">앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-851">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="44e61-852">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-852">Key</span></span>               | <span data-ttu-id="44e61-853">값</span><span class="sxs-lookup"><span data-stu-id="44e61-853">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="44e61-854">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-854">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="44e61-855"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-855">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-856">환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-856">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="44e61-857">[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="44e61-858">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-858">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="44e61-859">새 호스트 작성기가 [웹 호스트](xref:fundamentals/host/web-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `ASPNETCORE_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-859">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="44e61-860">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-860">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-861">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-861">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="44e61-862">접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-862">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="44e61-863">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="44e61-863">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="44e61-864">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="44e61-864">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="44e61-865">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="44e61-865">Command-line arguments.</span></span>

<span data-ttu-id="44e61-866">사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-866">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="44e61-867">이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-867">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="44e61-868">추가 환경 변수에서 앱 구성을 제공하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-868">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="44e61-869">지정된 접두사를 포함하는 환경 변수에서 다른 공급자의 값을 재정의할 수 있도록 하려면 `AddEnvironmentVariables`를 마지막으로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-869">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="44e61-870">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-870">**Example**</span></span>

<span data-ttu-id="44e61-871">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-871">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="44e61-872">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-872">Run the sample app.</span></span> <span data-ttu-id="44e61-873">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-873">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="44e61-874">환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-874">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="44e61-875">이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-875">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="44e61-876">앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-876">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="44e61-877">샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-877">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="44e61-878">앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-878">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="44e61-879">접두사</span><span class="sxs-lookup"><span data-stu-id="44e61-879">Prefixes</span></span>

<span data-ttu-id="44e61-880">`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-880">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="44e61-881">예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-881">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="44e61-882">구성 키-값 쌍이 생성되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-882">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="44e61-883">호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-883">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="44e61-884">환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-884">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-885">**연결 문자열 접두사**</span><span class="sxs-lookup"><span data-stu-id="44e61-885">**Connection string prefixes**</span></span>

<span data-ttu-id="44e61-886">구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-886">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="44e61-887">즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-887">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="44e61-888">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="44e61-888">Connection string prefix</span></span> | <span data-ttu-id="44e61-889">공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-889">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="44e61-890">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-890">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="44e61-891">MySQL</span><span class="sxs-lookup"><span data-stu-id="44e61-891">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="44e61-892">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="44e61-892">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="44e61-893">SQL Server</span><span class="sxs-lookup"><span data-stu-id="44e61-893">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="44e61-894">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-894">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="44e61-895">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-895">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="44e61-896">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-896">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="44e61-897">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="44e61-897">Environment variable key</span></span> | <span data-ttu-id="44e61-898">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="44e61-898">Converted configuration key</span></span> | <span data-ttu-id="44e61-899">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="44e61-899">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-900">구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-900">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-901">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="44e61-901">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="44e61-902">값: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="44e61-902">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-903">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="44e61-903">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="44e61-904">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="44e61-904">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="44e61-905">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="44e61-905">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="44e61-906">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="44e61-906">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="44e61-907">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-907">**Example**</span></span>

<span data-ttu-id="44e61-908">서버에 사용자 지정 연결 문자열 환경 변수가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-908">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="44e61-909">이름 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="44e61-909">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="44e61-910">값 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="44e61-910">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="44e61-911">`IConfiguration`이 삽입되고 `_config`라는 필드에 할당된 경우 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-911">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="44e61-912">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-912">File Configuration Provider</span></span>

<span data-ttu-id="44e61-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="44e61-914">다음 구성 공급자는 특정 파일 형식 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-914">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="44e61-915">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-915">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="44e61-916">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-916">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="44e61-917">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-917">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="44e61-918">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-918">INI Configuration Provider</span></span>

<span data-ttu-id="44e61-919"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-919">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-920">INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-920">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-921">콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-921">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="44e61-922">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-922">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-923">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-923">Whether the file is optional.</span></span>
* <span data-ttu-id="44e61-924">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-924">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="44e61-925">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="44e61-925">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="44e61-926">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-926">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="44e61-927">INI 구성 파일의 일반적인 예:</span><span class="sxs-lookup"><span data-stu-id="44e61-927">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="44e61-928">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-928">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-929">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-929">section0:key0</span></span>
* <span data-ttu-id="44e61-930">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-930">section0:key1</span></span>
* <span data-ttu-id="44e61-931">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="44e61-931">section1:subsection:key</span></span>
* <span data-ttu-id="44e61-932">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="44e61-932">section2:subsection0:key</span></span>
* <span data-ttu-id="44e61-933">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="44e61-933">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="44e61-934">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-934">JSON Configuration Provider</span></span>

<span data-ttu-id="44e61-935"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-935">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="44e61-936">JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-936">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-937">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-937">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-938">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-938">Whether the file is optional.</span></span>
* <span data-ttu-id="44e61-939">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-939">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="44e61-940">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="44e61-940">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="44e61-941">`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-941">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="44e61-942">이 메서드는 호출되면 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-942">The method is called to load configuration from:</span></span>

* <span data-ttu-id="44e61-943">*appsettings.json* &ndash; 이 파일을 먼저 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-943">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="44e61-944">파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-944">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="44e61-945">*appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-945">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="44e61-946">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-946">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="44e61-947">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-947">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="44e61-948">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="44e61-948">Environment variables.</span></span>
* <span data-ttu-id="44e61-949">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="44e61-949">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="44e61-950">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="44e61-950">Command-line arguments.</span></span>

<span data-ttu-id="44e61-951">JSON 구성 공급자를 먼저 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-951">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="44e61-952">따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-952">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="44e61-953">*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-953">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="44e61-954">**예제**</span><span class="sxs-lookup"><span data-stu-id="44e61-954">**Example**</span></span>

<span data-ttu-id="44e61-955">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-955">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="44e61-956">`AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-956">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="44e61-957">`AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-957">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="44e61-958">샘플 앱의 *appsettings.Development.json*의 경우 다음 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-958">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="44e61-959">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-959">Run the sample app.</span></span> <span data-ttu-id="44e61-960">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-960">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="44e61-961">출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-961">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="44e61-962">개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-962">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="44e61-963">프로덕션 환경에서 샘플 앱을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-963">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="44e61-964">*Properties/launchSettings.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-964">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="44e61-965">`ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-965">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="44e61-966">파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-966">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="44e61-967">*appsettings.Development.json*의 설정에서 더 이상 *appsettings.json*의 설정을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-967">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="44e61-968">`Logging:LogLevel:Default` 키의 로그 수준은 `Warning`입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-968">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="44e61-969">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-969">XML Configuration Provider</span></span>

<span data-ttu-id="44e61-970"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-970">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="44e61-971">XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-971">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-972">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-972">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-973">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-973">Whether the file is optional.</span></span>
* <span data-ttu-id="44e61-974">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="44e61-974">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="44e61-975">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="44e61-975">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="44e61-976">구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-976">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="44e61-977">파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-977">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="44e61-978">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-978">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="44e61-979">XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-979">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="44e61-980">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-980">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-981">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-981">section0:key0</span></span>
* <span data-ttu-id="44e61-982">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-982">section0:key1</span></span>
* <span data-ttu-id="44e61-983">section1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-983">section1:key0</span></span>
* <span data-ttu-id="44e61-984">section1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-984">section1:key1</span></span>

<span data-ttu-id="44e61-985">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-985">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="44e61-986">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-986">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-987">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-987">section:section0:key:key0</span></span>
* <span data-ttu-id="44e61-988">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-988">section:section0:key:key1</span></span>
* <span data-ttu-id="44e61-989">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-989">section:section1:key:key0</span></span>
* <span data-ttu-id="44e61-990">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-990">section:section1:key:key1</span></span>

<span data-ttu-id="44e61-991">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-991">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="44e61-992">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-992">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="44e61-993">key:attribute</span><span class="sxs-lookup"><span data-stu-id="44e61-993">key:attribute</span></span>
* <span data-ttu-id="44e61-994">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="44e61-994">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="44e61-995">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-995">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="44e61-996"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-996">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="44e61-997">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="44e61-997">The key is the file name.</span></span> <span data-ttu-id="44e61-998">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-998">The value contains the file's contents.</span></span> <span data-ttu-id="44e61-999">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-999">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="44e61-1000">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1000">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="44e61-1001">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1001">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="44e61-1002">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1002">Overloads permit specifying:</span></span>

* <span data-ttu-id="44e61-1003">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="44e61-1003">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="44e61-1004">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="44e61-1004">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="44e61-1005">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1005">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="44e61-1006">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1006">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="44e61-1007">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1007">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="44e61-1008">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-1008">Memory Configuration Provider</span></span>

<span data-ttu-id="44e61-1009"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1009">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="44e61-1010">메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1010">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="44e61-1011">`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1011">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="44e61-1012">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1012">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="44e61-1013">다음 예제에서는 구성 사전이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1013">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="44e61-1014">사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1014">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="44e61-1015">GetValue</span><span class="sxs-lookup"><span data-stu-id="44e61-1015">GetValue</span></span>

<span data-ttu-id="44e61-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 하나의 값을 추출하고 해당 값을 지정된 비컬렉션 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="44e61-1017">오버로드는 기본값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1017">An overload accepts a default value.</span></span>

<span data-ttu-id="44e61-1018">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="44e61-1018">The following example:</span></span>

* <span data-ttu-id="44e61-1019">`NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1019">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="44e61-1020">구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1020">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="44e61-1021">값을 `int` 형식으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1021">Types the value as an `int`.</span></span>
* <span data-ttu-id="44e61-1022">페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1022">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="44e61-1023">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="44e61-1023">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="44e61-1024">이어지는 예제에서는 다음 JSON 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-1024">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="44e61-1025">네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1025">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="44e61-1026">파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1026">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="44e61-1027">section0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-1027">section0:key0</span></span>
* <span data-ttu-id="44e61-1028">section0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-1028">section0:key1</span></span>
* <span data-ttu-id="44e61-1029">section1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-1029">section1:key0</span></span>
* <span data-ttu-id="44e61-1030">section1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-1030">section1:key1</span></span>
* <span data-ttu-id="44e61-1031">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-1031">section2:subsection0:key0</span></span>
* <span data-ttu-id="44e61-1032">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-1032">section2:subsection0:key1</span></span>
* <span data-ttu-id="44e61-1033">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="44e61-1033">section2:subsection1:key0</span></span>
* <span data-ttu-id="44e61-1034">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="44e61-1034">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="44e61-1035">GetSection</span><span class="sxs-lookup"><span data-stu-id="44e61-1035">GetSection</span></span>

<span data-ttu-id="44e61-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="44e61-1037">`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1037">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="44e61-1038">`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1038">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="44e61-1039">마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1039">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="44e61-1040">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1040">`GetSection` never returns `null`.</span></span> <span data-ttu-id="44e61-1041">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1041">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="44e61-1042">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1042">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="44e61-1043">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1043">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="44e61-1044">GetChildren</span><span class="sxs-lookup"><span data-stu-id="44e61-1044">GetChildren</span></span>

<span data-ttu-id="44e61-1045">`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1045">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="44e61-1046">있음</span><span class="sxs-lookup"><span data-stu-id="44e61-1046">Exists</span></span>

<span data-ttu-id="44e61-1047">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1047">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="44e61-1048">예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1048">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="44e61-1049">클래스에 바인딩</span><span class="sxs-lookup"><span data-stu-id="44e61-1049">Bind to a class</span></span>

<span data-ttu-id="44e61-1050">‘옵션 패턴’을 사용하여 관련 설정 그룹을 나타내는 클래스에 구성을 바인딩할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-1050">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="44e61-1051">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-1051">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="44e61-1052">구성 값이 문자열로 반환되지만, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>를 호출하면 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1052">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="44e61-1053">바인더는 제공된 형식의 모든 공용 읽기/쓰기 속성에 값을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1053">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="44e61-1054">필드가 바인딩되지 **않았습니다**.</span><span class="sxs-lookup"><span data-stu-id="44e61-1054">Fields are **not** bound.</span></span>

<span data-ttu-id="44e61-1055">샘플 앱은 `Starship` 모델(*Models/Starship.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1055">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="44e61-1056">샘플 앱이 JSON 구성 공급자를 사용하여 구성을 로드하는 경우 *starship.json* 파일의 `starship` 섹션에서 구성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1056">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="44e61-1057">다음 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1057">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="44e61-1058">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-1058">Key</span></span>                   | <span data-ttu-id="44e61-1059">값</span><span class="sxs-lookup"><span data-stu-id="44e61-1059">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="44e61-1060">starship:name</span><span class="sxs-lookup"><span data-stu-id="44e61-1060">starship:name</span></span>         | <span data-ttu-id="44e61-1061">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="44e61-1061">USS Enterprise</span></span>                                    |
| <span data-ttu-id="44e61-1062">starship:registry</span><span class="sxs-lookup"><span data-stu-id="44e61-1062">starship:registry</span></span>     | <span data-ttu-id="44e61-1063">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="44e61-1063">NCC-1701</span></span>                                          |
| <span data-ttu-id="44e61-1064">starship:class</span><span class="sxs-lookup"><span data-stu-id="44e61-1064">starship:class</span></span>        | <span data-ttu-id="44e61-1065">Constitution</span><span class="sxs-lookup"><span data-stu-id="44e61-1065">Constitution</span></span>                                      |
| <span data-ttu-id="44e61-1066">starship:length</span><span class="sxs-lookup"><span data-stu-id="44e61-1066">starship:length</span></span>       | <span data-ttu-id="44e61-1067">304.8</span><span class="sxs-lookup"><span data-stu-id="44e61-1067">304.8</span></span>                                             |
| <span data-ttu-id="44e61-1068">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="44e61-1068">starship:commissioned</span></span> | <span data-ttu-id="44e61-1069">False</span><span class="sxs-lookup"><span data-stu-id="44e61-1069">False</span></span>                                             |
| <span data-ttu-id="44e61-1070">trademark</span><span class="sxs-lookup"><span data-stu-id="44e61-1070">trademark</span></span>             | <span data-ttu-id="44e61-1071">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="44e61-1071">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="44e61-1072">샘플 앱은 `starship` 키를 사용하여 `GetSection`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1072">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="44e61-1073">`starship` 키-값 쌍은 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1073">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="44e61-1074">`Bind` 메서드는 `Starship` 클래스의 인스턴스를 전달하는 하위 섹션에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1074">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="44e61-1075">인스턴스 값을 바인딩한 후 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1075">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="44e61-1076">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="44e61-1076">Bind to an object graph</span></span>

<span data-ttu-id="44e61-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="44e61-1078">단순 개체 바인딩과 마찬가지로 공용 읽기/쓰기 속성만 바인딩되었습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1078">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="44e61-1079">다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1079">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="44e61-1080">샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1080">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="44e61-1081">`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1081">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="44e61-1082">바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1082">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="44e61-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="44e61-1084">`Get<T>`가 `Bind`보다 사용하기 더 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1084">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="44e61-1085">다음 코드에서는 앞의 예제에 `Get<T>`를 사용하여 바인딩된 인스턴스가 렌더링에 사용되는 속성에 바로 할당될 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1085">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="44e61-1086">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="44e61-1086">Bind an array to a class</span></span>

<span data-ttu-id="44e61-1087">다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다. </span><span class="sxs-lookup"><span data-stu-id="44e61-1087">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="44e61-1088"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1088">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="44e61-1089">숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1089">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="44e61-1090">바인딩은 규칙에 따라 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1090">Binding is provided by convention.</span></span> <span data-ttu-id="44e61-1091">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1091">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="44e61-1092">**메모리 내 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="44e61-1092">**In-memory array processing**</span></span>

<span data-ttu-id="44e61-1093">다음 표에 표시된 구성 키 및 값을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-1093">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="44e61-1094">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-1094">Key</span></span>             | <span data-ttu-id="44e61-1095">값</span><span class="sxs-lookup"><span data-stu-id="44e61-1095">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="44e61-1096">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="44e61-1096">array:entries:0</span></span> | <span data-ttu-id="44e61-1097">value0</span><span class="sxs-lookup"><span data-stu-id="44e61-1097">value0</span></span> |
| <span data-ttu-id="44e61-1098">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="44e61-1098">array:entries:1</span></span> | <span data-ttu-id="44e61-1099">value1</span><span class="sxs-lookup"><span data-stu-id="44e61-1099">value1</span></span> |
| <span data-ttu-id="44e61-1100">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="44e61-1100">array:entries:2</span></span> | <span data-ttu-id="44e61-1101">value2</span><span class="sxs-lookup"><span data-stu-id="44e61-1101">value2</span></span> |
| <span data-ttu-id="44e61-1102">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="44e61-1102">array:entries:4</span></span> | <span data-ttu-id="44e61-1103">value4</span><span class="sxs-lookup"><span data-stu-id="44e61-1103">value4</span></span> |
| <span data-ttu-id="44e61-1104">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="44e61-1104">array:entries:5</span></span> | <span data-ttu-id="44e61-1105">value5</span><span class="sxs-lookup"><span data-stu-id="44e61-1105">value5</span></span> |

<span data-ttu-id="44e61-1106">메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1106">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="44e61-1107">배열은 인덱스 &num;3에 대한 값을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1107">The array skips a value for index &num;3.</span></span> <span data-ttu-id="44e61-1108">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1108">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="44e61-1109">샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1109">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="44e61-1110">구성 데이터는 개체에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1110">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="44e61-1111">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문도 사용할 수 있으며, 이 경우 코드가 더 간결해집니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1111">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="44e61-1112">바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1112">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="44e61-1113">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="44e61-1113">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="44e61-1114">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="44e61-1114">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="44e61-1115">0</span><span class="sxs-lookup"><span data-stu-id="44e61-1115">0</span></span>                            | <span data-ttu-id="44e61-1116">value0</span><span class="sxs-lookup"><span data-stu-id="44e61-1116">value0</span></span>                       |
| <span data-ttu-id="44e61-1117">1</span><span class="sxs-lookup"><span data-stu-id="44e61-1117">1</span></span>                            | <span data-ttu-id="44e61-1118">value1</span><span class="sxs-lookup"><span data-stu-id="44e61-1118">value1</span></span>                       |
| <span data-ttu-id="44e61-1119">2</span><span class="sxs-lookup"><span data-stu-id="44e61-1119">2</span></span>                            | <span data-ttu-id="44e61-1120">value2</span><span class="sxs-lookup"><span data-stu-id="44e61-1120">value2</span></span>                       |
| <span data-ttu-id="44e61-1121">3</span><span class="sxs-lookup"><span data-stu-id="44e61-1121">3</span></span>                            | <span data-ttu-id="44e61-1122">value4</span><span class="sxs-lookup"><span data-stu-id="44e61-1122">value4</span></span>                       |
| <span data-ttu-id="44e61-1123">4</span><span class="sxs-lookup"><span data-stu-id="44e61-1123">4</span></span>                            | <span data-ttu-id="44e61-1124">value5</span><span class="sxs-lookup"><span data-stu-id="44e61-1124">value5</span></span>                       |

<span data-ttu-id="44e61-1125">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1125">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="44e61-1126">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1126">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="44e61-1127">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1127">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="44e61-1128">인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1128">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="44e61-1129">샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1129">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="44e61-1130">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="44e61-1130">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="44e61-1131">`ConfigureAppConfiguration`의 경우</span><span class="sxs-lookup"><span data-stu-id="44e61-1131">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="44e61-1132">표에 표시된 키-값 쌍이 구성으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1132">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="44e61-1133">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-1133">Key</span></span>             | <span data-ttu-id="44e61-1134">값</span><span class="sxs-lookup"><span data-stu-id="44e61-1134">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="44e61-1135">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="44e61-1135">array:entries:3</span></span> | <span data-ttu-id="44e61-1136">value3</span><span class="sxs-lookup"><span data-stu-id="44e61-1136">value3</span></span> |

<span data-ttu-id="44e61-1137">JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1137">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="44e61-1138">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="44e61-1138">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="44e61-1139">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="44e61-1139">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="44e61-1140">0</span><span class="sxs-lookup"><span data-stu-id="44e61-1140">0</span></span>                            | <span data-ttu-id="44e61-1141">value0</span><span class="sxs-lookup"><span data-stu-id="44e61-1141">value0</span></span>                       |
| <span data-ttu-id="44e61-1142">1</span><span class="sxs-lookup"><span data-stu-id="44e61-1142">1</span></span>                            | <span data-ttu-id="44e61-1143">value1</span><span class="sxs-lookup"><span data-stu-id="44e61-1143">value1</span></span>                       |
| <span data-ttu-id="44e61-1144">2</span><span class="sxs-lookup"><span data-stu-id="44e61-1144">2</span></span>                            | <span data-ttu-id="44e61-1145">value2</span><span class="sxs-lookup"><span data-stu-id="44e61-1145">value2</span></span>                       |
| <span data-ttu-id="44e61-1146">3</span><span class="sxs-lookup"><span data-stu-id="44e61-1146">3</span></span>                            | <span data-ttu-id="44e61-1147">value3</span><span class="sxs-lookup"><span data-stu-id="44e61-1147">value3</span></span>                       |
| <span data-ttu-id="44e61-1148">4</span><span class="sxs-lookup"><span data-stu-id="44e61-1148">4</span></span>                            | <span data-ttu-id="44e61-1149">value4</span><span class="sxs-lookup"><span data-stu-id="44e61-1149">value4</span></span>                       |
| <span data-ttu-id="44e61-1150">5</span><span class="sxs-lookup"><span data-stu-id="44e61-1150">5</span></span>                            | <span data-ttu-id="44e61-1151">value5</span><span class="sxs-lookup"><span data-stu-id="44e61-1151">value5</span></span>                       |

<span data-ttu-id="44e61-1152">**JSON 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="44e61-1152">**JSON array processing**</span></span>

<span data-ttu-id="44e61-1153">JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1153">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="44e61-1154">다음 구성 파일에서 `subsection`은 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1154">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="44e61-1155">JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1155">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="44e61-1156">Key</span><span class="sxs-lookup"><span data-stu-id="44e61-1156">Key</span></span>                     | <span data-ttu-id="44e61-1157">값</span><span class="sxs-lookup"><span data-stu-id="44e61-1157">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="44e61-1158">json_array:key</span><span class="sxs-lookup"><span data-stu-id="44e61-1158">json_array:key</span></span>          | <span data-ttu-id="44e61-1159">valueA</span><span class="sxs-lookup"><span data-stu-id="44e61-1159">valueA</span></span> |
| <span data-ttu-id="44e61-1160">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="44e61-1160">json_array:subsection:0</span></span> | <span data-ttu-id="44e61-1161">valueB</span><span class="sxs-lookup"><span data-stu-id="44e61-1161">valueB</span></span> |
| <span data-ttu-id="44e61-1162">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="44e61-1162">json_array:subsection:1</span></span> | <span data-ttu-id="44e61-1163">valueC</span><span class="sxs-lookup"><span data-stu-id="44e61-1163">valueC</span></span> |
| <span data-ttu-id="44e61-1164">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="44e61-1164">json_array:subsection:2</span></span> | <span data-ttu-id="44e61-1165">valueD</span><span class="sxs-lookup"><span data-stu-id="44e61-1165">valueD</span></span> |

<span data-ttu-id="44e61-1166">샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1166">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="44e61-1167">바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1167">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="44e61-1168">하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1168">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="44e61-1169">`JsonArrayExample.Subsection` 인덱스</span><span class="sxs-lookup"><span data-stu-id="44e61-1169">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="44e61-1170">`JsonArrayExample.Subsection` 값</span><span class="sxs-lookup"><span data-stu-id="44e61-1170">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="44e61-1171">0</span><span class="sxs-lookup"><span data-stu-id="44e61-1171">0</span></span>                                   | <span data-ttu-id="44e61-1172">valueB</span><span class="sxs-lookup"><span data-stu-id="44e61-1172">valueB</span></span>                              |
| <span data-ttu-id="44e61-1173">1</span><span class="sxs-lookup"><span data-stu-id="44e61-1173">1</span></span>                                   | <span data-ttu-id="44e61-1174">valueC</span><span class="sxs-lookup"><span data-stu-id="44e61-1174">valueC</span></span>                              |
| <span data-ttu-id="44e61-1175">2</span><span class="sxs-lookup"><span data-stu-id="44e61-1175">2</span></span>                                   | <span data-ttu-id="44e61-1176">valueD</span><span class="sxs-lookup"><span data-stu-id="44e61-1176">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="44e61-1177">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="44e61-1177">Custom configuration provider</span></span>

<span data-ttu-id="44e61-1178">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1178">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="44e61-1179">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1179">The provider has the following characteristics:</span></span>

* <span data-ttu-id="44e61-1180">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1180">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="44e61-1181">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-1181">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="44e61-1182">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1182">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="44e61-1183">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1183">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="44e61-1184">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1184">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="44e61-1185">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1185">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="44e61-1186">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-1186">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="44e61-1187">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1187">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="44e61-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="44e61-1189"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1189">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="44e61-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="44e61-1191"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1191">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="44e61-1192">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1192">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="44e61-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="44e61-1194">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1194">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="44e61-1195">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="44e61-1195">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="44e61-1196">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1196">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="44e61-1197">시작하는 동안 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="44e61-1197">Access configuration during startup</span></span>

<span data-ttu-id="44e61-1198">`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1198">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="44e61-1199">`Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1199">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="44e61-1200">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-1200">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="44e61-1201">Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="44e61-1201">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="44e61-1202">Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1202">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="44e61-1203">Razor 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="44e61-1203">In a Razor Pages page:</span></span>

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

<span data-ttu-id="44e61-1204">MVC 뷰에서:</span><span class="sxs-lookup"><span data-stu-id="44e61-1204">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="44e61-1205">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="44e61-1205">Add configuration from an external assembly</span></span>

<span data-ttu-id="44e61-1206"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="44e61-1206">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="44e61-1207">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="44e61-1207">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44e61-1208">추가 자료</span><span class="sxs-lookup"><span data-stu-id="44e61-1208">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
