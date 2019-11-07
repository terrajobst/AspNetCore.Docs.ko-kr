---
title: ASP.NET Core의 구성
author: guardrex
description: 구성 API를 사용하여 ASP.NET Core 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 9f0ad2791e504a0ff46daad07054b6bf909a546a
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634085"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="50e7d-103">ASP.NET Core의 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="50e7d-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="50e7d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50e7d-105">ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다. </span><span class="sxs-lookup"><span data-stu-id="50e7d-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="50e7d-106">구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="50e7d-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="50e7d-107">Azure Key Vault</span></span>
* <span data-ttu-id="50e7d-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="50e7d-108">Azure App Configuration</span></span>
* <span data-ttu-id="50e7d-109">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="50e7d-109">Command-line arguments</span></span>
* <span data-ttu-id="50e7d-110">사용자 지정 공급자(설치 또는 생성된)</span><span class="sxs-lookup"><span data-stu-id="50e7d-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="50e7d-111">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="50e7d-111">Directory files</span></span>
* <span data-ttu-id="50e7d-112">환경 변수</span><span class="sxs-lookup"><span data-stu-id="50e7d-112">Environment variables</span></span>
* <span data-ttu-id="50e7d-113">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="50e7d-113">In-memory .NET objects</span></span>
* <span data-ttu-id="50e7d-114">설정 파일</span><span class="sxs-lookup"><span data-stu-id="50e7d-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="50e7d-115">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 프레임워크에 의해 암시적으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50e7d-116">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="50e7d-117">샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:</span><span class="sxs-lookup"><span data-stu-id="50e7d-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="50e7d-118">‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다. </span><span class="sxs-lookup"><span data-stu-id="50e7d-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="50e7d-119">옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="50e7d-120">자세한 내용은 <xref:fundamentals/configuration/options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="50e7d-121">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="50e7d-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="50e7d-122">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-122">Host versus app configuration</span></span>

<span data-ttu-id="50e7d-123">앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="50e7d-124">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="50e7d-125">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="50e7d-126">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="50e7d-127">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="50e7d-128">기본 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="50e7d-129">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="50e7d-130">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="50e7d-131">다음은 [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="50e7d-132">[웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 기본 구성에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.2 버전](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="50e7d-133">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="50e7d-134">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `DOTNET_`를 접두사로 사용하는 환경 변수(예: `DOTNET_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="50e7d-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="50e7d-135">구성 키-값 쌍이 로드되면 접두사(`DOTNET_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="50e7d-136">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="50e7d-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="50e7d-137">웹 호스트 기본 구성이 설정됩니다(`ConfigureWebHostDefaults`).</span><span class="sxs-lookup"><span data-stu-id="50e7d-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="50e7d-138">Kestrel은 웹 서버로 사용되며 앱의 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="50e7d-139">호스트 필터링 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="50e7d-140">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 전달된 헤더 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="50e7d-141">IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-141">Enable IIS integration.</span></span>
* <span data-ttu-id="50e7d-142">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="50e7d-143">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="50e7d-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="50e7d-144">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="50e7d-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="50e7d-145">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="50e7d-146">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="50e7d-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="50e7d-147">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="50e7d-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50e7d-148">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="50e7d-149">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="50e7d-150">[웹호스트](xref:fundamentals/host/web-host)를 사용하는 앱에는 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="50e7d-151">[제네릭 호스트](xref:fundamentals/host/generic-host) 사용 시 기본 구성에 대한 자세한 내용은 [이 토픽의 최신 버전](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="50e7d-152">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="50e7d-153">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `ASPNETCORE_`를 접두사로 사용하는 환경 변수(예: `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="50e7d-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="50e7d-154">구성 키-값 쌍이 로드되면 접두사(`ASPNETCORE_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="50e7d-155">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="50e7d-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="50e7d-156">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="50e7d-157">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="50e7d-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="50e7d-158">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="50e7d-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="50e7d-159">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="50e7d-160">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="50e7d-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="50e7d-161">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="50e7d-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="50e7d-162">보안</span><span class="sxs-lookup"><span data-stu-id="50e7d-162">Security</span></span>

<span data-ttu-id="50e7d-163">중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="50e7d-164">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="50e7d-165">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="50e7d-166">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="50e7d-167">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="50e7d-168"><xref:security/app-secrets> &ndash; 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="50e7d-169">비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="50e7d-170">파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="50e7d-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="50e7d-172">자세한 내용은 <xref:security/key-vault-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="50e7d-173">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="50e7d-173">Hierarchical configuration data</span></span>

<span data-ttu-id="50e7d-174">구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="50e7d-175">다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="50e7d-176">파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="50e7d-177">콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="50e7d-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-178">section0:key0</span></span>
* <span data-ttu-id="50e7d-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-179">section0:key1</span></span>
* <span data-ttu-id="50e7d-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-180">section1:key0</span></span>
* <span data-ttu-id="50e7d-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-181">section1:key1</span></span>

<span data-ttu-id="50e7d-182">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="50e7d-183">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="50e7d-184">규칙</span><span class="sxs-lookup"><span data-stu-id="50e7d-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="50e7d-185">소스 및 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-185">Sources and providers</span></span>

<span data-ttu-id="50e7d-186">앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="50e7d-187">변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="50e7d-188">예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="50e7d-189"><xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="50e7d-190"><xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="50e7d-191">구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="50e7d-192">키</span><span class="sxs-lookup"><span data-stu-id="50e7d-192">Keys</span></span>

<span data-ttu-id="50e7d-193">구성키는 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="50e7d-194">키는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-194">Keys are case-insensitive.</span></span> <span data-ttu-id="50e7d-195">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="50e7d-196">같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="50e7d-197">계층적 키</span><span class="sxs-lookup"><span data-stu-id="50e7d-197">Hierarchical keys</span></span>
  * <span data-ttu-id="50e7d-198">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="50e7d-199">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="50e7d-200">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="50e7d-201">Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="50e7d-202">비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="50e7d-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="50e7d-204">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="50e7d-205">값</span><span class="sxs-lookup"><span data-stu-id="50e7d-205">Values</span></span>

<span data-ttu-id="50e7d-206">구성 값은 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="50e7d-207">값은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-207">Values are strings.</span></span>
* <span data-ttu-id="50e7d-208">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="50e7d-209">공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-209">Providers</span></span>

<span data-ttu-id="50e7d-210">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="50e7d-211">공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-211">Provider</span></span> | <span data-ttu-id="50e7d-212">다음에서 구성 제공&hellip;</span><span class="sxs-lookup"><span data-stu-id="50e7d-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="50e7d-213">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="50e7d-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="50e7d-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="50e7d-214">Azure Key Vault</span></span> |
| <span data-ttu-id="50e7d-215">[Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="50e7d-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="50e7d-216">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="50e7d-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="50e7d-217">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="50e7d-218">명령줄 매개 변수</span><span class="sxs-lookup"><span data-stu-id="50e7d-218">Command-line parameters</span></span> |
| [<span data-ttu-id="50e7d-219">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="50e7d-220">사용자 지정 소스</span><span class="sxs-lookup"><span data-stu-id="50e7d-220">Custom source</span></span> |
| [<span data-ttu-id="50e7d-221">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="50e7d-222">환경 변수</span><span class="sxs-lookup"><span data-stu-id="50e7d-222">Environment variables</span></span> |
| [<span data-ttu-id="50e7d-223">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="50e7d-224">파일(INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="50e7d-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="50e7d-225">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="50e7d-226">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="50e7d-226">Directory files</span></span> |
| [<span data-ttu-id="50e7d-227">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="50e7d-228">메모리 내 컬렉션</span><span class="sxs-lookup"><span data-stu-id="50e7d-228">In-memory collections</span></span> |
| <span data-ttu-id="50e7d-229">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="50e7d-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="50e7d-230">사용자 프로필 디렉터리의 파일</span><span class="sxs-lookup"><span data-stu-id="50e7d-230">File in the user profile directory</span></span> |

<span data-ttu-id="50e7d-231">시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="50e7d-232">이 항목의 구성 공급자는 코드에서 정렬할 수 있는 순서가 아니라 사전순으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="50e7d-233">코드에서는 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="50e7d-234">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="50e7d-235">파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)</span><span class="sxs-lookup"><span data-stu-id="50e7d-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="50e7d-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="50e7d-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="50e7d-237">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)</span><span class="sxs-lookup"><span data-stu-id="50e7d-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="50e7d-238">환경 변수</span><span class="sxs-lookup"><span data-stu-id="50e7d-238">Environment variables</span></span>
1. <span data-ttu-id="50e7d-239">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="50e7d-239">Command-line arguments</span></span>

<span data-ttu-id="50e7d-240">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="50e7d-241">`CreateDefaultBuilder`로 새 호스트 작성기를 초기화할 때 이전 공급자 시퀀스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="50e7d-242">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="50e7d-243">ConfigureHostConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="50e7d-244">호스트 작성기를 구성하려면 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출하고 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="50e7d-245">`ConfigureHostConfiguration`은 나중에 빌드 프로세스에서 사용하기 위해 <xref:Microsoft.Extensions.Hosting.IHostEnvironment>를 초기화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="50e7d-246">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="50e7d-247">UseConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="50e7d-248">호스트 작성기를 구성하려면 구성과 함께 호스트 작성기에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="50e7d-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="50e7d-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="50e7d-250">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="50e7d-251">명령줄 인수를 사용하여 이전 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="50e7d-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="50e7d-252">명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="50e7d-253">CreateDefaultBuilder에 의해 추가된 공급자 제거</span><span class="sxs-lookup"><span data-stu-id="50e7d-253">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="50e7d-254">`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-254">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="50e7d-255">앱 시작 중 구성 사용</span><span class="sxs-lookup"><span data-stu-id="50e7d-255">Consume configuration during app startup</span></span>

<span data-ttu-id="50e7d-256">`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-256">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="50e7d-257">자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-257">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="50e7d-258">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-258">Command-line Configuration Provider</span></span>

<span data-ttu-id="50e7d-259"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-259">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="50e7d-260">명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-260">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="50e7d-261">`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-261">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="50e7d-262">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-262">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="50e7d-263">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-263">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="50e7d-264">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-264">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="50e7d-265">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="50e7d-265">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="50e7d-266">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="50e7d-266">Environment variables.</span></span>

<span data-ttu-id="50e7d-267">`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-267">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="50e7d-268">따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-268">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="50e7d-269">`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-269">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="50e7d-270">따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-270">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="50e7d-271">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-271">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="50e7d-272">추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-272">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="50e7d-273">**예제**</span><span class="sxs-lookup"><span data-stu-id="50e7d-273">**Example**</span></span>

<span data-ttu-id="50e7d-274">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-274">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="50e7d-275">프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-275">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="50e7d-276">`dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).</span><span class="sxs-lookup"><span data-stu-id="50e7d-276">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="50e7d-277">앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-277">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="50e7d-278">`dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-278">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="50e7d-279">인수</span><span class="sxs-lookup"><span data-stu-id="50e7d-279">Arguments</span></span>

<span data-ttu-id="50e7d-280">값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-280">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="50e7d-281">등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="50e7d-281">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="50e7d-282">키 접두사</span><span class="sxs-lookup"><span data-stu-id="50e7d-282">Key prefix</span></span>               | <span data-ttu-id="50e7d-283">예</span><span class="sxs-lookup"><span data-stu-id="50e7d-283">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="50e7d-284">접두사 없음</span><span class="sxs-lookup"><span data-stu-id="50e7d-284">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="50e7d-285">대시 2개(`--`)</span><span class="sxs-lookup"><span data-stu-id="50e7d-285">Two dashes (`--`)</span></span>        | <span data-ttu-id="50e7d-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="50e7d-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="50e7d-287">슬래시(`/`)</span><span class="sxs-lookup"><span data-stu-id="50e7d-287">Forward slash (`/`)</span></span>      | <span data-ttu-id="50e7d-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="50e7d-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="50e7d-289">같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-289">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="50e7d-290">명령 예:</span><span class="sxs-lookup"><span data-stu-id="50e7d-290">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="50e7d-291">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="50e7d-291">Switch mappings</span></span>

<span data-ttu-id="50e7d-292">스위치 매핑은 키 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-292">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="50e7d-293"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-293">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="50e7d-294">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-294">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="50e7d-295">사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-295">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="50e7d-296">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-296">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="50e7d-297">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="50e7d-297">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="50e7d-298">스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-298">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="50e7d-299">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-299">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="50e7d-300">스위치 매핑 사전을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-300">Create a switch mappings dictionary.</span></span> <span data-ttu-id="50e7d-301">다음 예에서는 두 개의 스위치 매핑이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-301">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="50e7d-302">호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-302">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="50e7d-303">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-303">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="50e7d-304">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-304">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="50e7d-305">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-305">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="50e7d-306">생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-306">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="50e7d-307">Key</span><span class="sxs-lookup"><span data-stu-id="50e7d-307">Key</span></span>       | <span data-ttu-id="50e7d-308">값</span><span class="sxs-lookup"><span data-stu-id="50e7d-308">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="50e7d-309">앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-309">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="50e7d-310">앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-310">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="50e7d-311">Key</span><span class="sxs-lookup"><span data-stu-id="50e7d-311">Key</span></span>               | <span data-ttu-id="50e7d-312">값</span><span class="sxs-lookup"><span data-stu-id="50e7d-312">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="50e7d-313">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-313">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="50e7d-314"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-314">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="50e7d-315">환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-315">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="50e7d-316">[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="50e7d-317">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-317">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="50e7d-318">새 호스트 작성기가 [제네릭 호스트](xref:fundamentals/host/generic-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `DOTNET_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="50e7d-319">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50e7d-320">새 호스트 작성기가 [웹 호스트](xref:fundamentals/host/web-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `ASPNETCORE_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-320">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="50e7d-321">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-321">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="50e7d-322">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-322">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="50e7d-323">접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-323">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="50e7d-324">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="50e7d-324">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="50e7d-325">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="50e7d-325">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="50e7d-326">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="50e7d-326">Command-line arguments.</span></span>

<span data-ttu-id="50e7d-327">사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-327">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="50e7d-328">이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-328">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="50e7d-329">추가 환경 변수에서 앱 구성을 제공해야 하는 경우에는 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-329">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
}
```

<span data-ttu-id="50e7d-330">**예제**</span><span class="sxs-lookup"><span data-stu-id="50e7d-330">**Example**</span></span>

<span data-ttu-id="50e7d-331">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-331">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="50e7d-332">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-332">Run the sample app.</span></span> <span data-ttu-id="50e7d-333">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-333">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="50e7d-334">환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-334">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="50e7d-335">이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-335">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="50e7d-336">앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-336">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="50e7d-337">샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-337">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="50e7d-338">앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-338">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="50e7d-339">접두사</span><span class="sxs-lookup"><span data-stu-id="50e7d-339">Prefixes</span></span>

<span data-ttu-id="50e7d-340">`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-340">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="50e7d-341">예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-341">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="50e7d-342">구성 키-값 쌍이 생성되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-342">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="50e7d-343">호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-343">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="50e7d-344">환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-344">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="50e7d-345">**연결 문자열 접두사**</span><span class="sxs-lookup"><span data-stu-id="50e7d-345">**Connection string prefixes**</span></span>

<span data-ttu-id="50e7d-346">구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-346">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="50e7d-347">즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-347">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="50e7d-348">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="50e7d-348">Connection string prefix</span></span> | <span data-ttu-id="50e7d-349">공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-349">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="50e7d-350">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-350">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="50e7d-351">MySQL</span><span class="sxs-lookup"><span data-stu-id="50e7d-351">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="50e7d-352">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="50e7d-352">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="50e7d-353">SQL Server</span><span class="sxs-lookup"><span data-stu-id="50e7d-353">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="50e7d-354">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-354">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="50e7d-355">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-355">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="50e7d-356">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-356">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="50e7d-357">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="50e7d-357">Environment variable key</span></span> | <span data-ttu-id="50e7d-358">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="50e7d-358">Converted configuration key</span></span> | <span data-ttu-id="50e7d-359">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="50e7d-359">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="50e7d-360">구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-360">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="50e7d-361">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="50e7d-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="50e7d-362">값: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="50e7d-362">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="50e7d-363">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="50e7d-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="50e7d-364">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="50e7d-364">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="50e7d-365">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="50e7d-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="50e7d-366">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="50e7d-366">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="50e7d-367">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-367">File Configuration Provider</span></span>

<span data-ttu-id="50e7d-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="50e7d-369">다음 구성 공급자는 특정 파일 형식 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-369">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="50e7d-370">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-370">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="50e7d-371">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-371">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="50e7d-372">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-372">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="50e7d-373">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-373">INI Configuration Provider</span></span>

<span data-ttu-id="50e7d-374"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-374">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="50e7d-375">INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-375">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="50e7d-376">콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-376">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="50e7d-377">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="50e7d-378">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="50e7d-378">Whether the file is optional.</span></span>
* <span data-ttu-id="50e7d-379">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="50e7d-379">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="50e7d-380">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="50e7d-380">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="50e7d-381">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="50e7d-382">INI 구성 파일의 일반적인 예:</span><span class="sxs-lookup"><span data-stu-id="50e7d-382">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="50e7d-383">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-383">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="50e7d-384">section0:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-384">section0:key0</span></span>
* <span data-ttu-id="50e7d-385">section0:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-385">section0:key1</span></span>
* <span data-ttu-id="50e7d-386">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="50e7d-386">section1:subsection:key</span></span>
* <span data-ttu-id="50e7d-387">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="50e7d-387">section2:subsection0:key</span></span>
* <span data-ttu-id="50e7d-388">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="50e7d-388">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="50e7d-389">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-389">JSON Configuration Provider</span></span>

<span data-ttu-id="50e7d-390"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-390">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="50e7d-391">JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-391">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="50e7d-392">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-392">Overloads permit specifying:</span></span>

* <span data-ttu-id="50e7d-393">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="50e7d-393">Whether the file is optional.</span></span>
* <span data-ttu-id="50e7d-394">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="50e7d-394">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="50e7d-395">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="50e7d-395">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="50e7d-396">`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-396">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="50e7d-397">이 메서드는 호출되면 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-397">The method is called to load configuration from:</span></span>

* <span data-ttu-id="50e7d-398">*appsettings.json* &ndash; 이 파일을 먼저 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-398">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="50e7d-399">파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-399">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="50e7d-400">*appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-400">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="50e7d-401">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-401">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="50e7d-402">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-402">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="50e7d-403">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="50e7d-403">Environment variables.</span></span>
* <span data-ttu-id="50e7d-404">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="50e7d-404">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="50e7d-405">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="50e7d-405">Command-line arguments.</span></span>

<span data-ttu-id="50e7d-406">JSON 구성 공급자를 먼저 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-406">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="50e7d-407">따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-407">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="50e7d-408">*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-408">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="50e7d-409">**예제**</span><span class="sxs-lookup"><span data-stu-id="50e7d-409">**Example**</span></span>

<span data-ttu-id="50e7d-410">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="50e7d-411">구성은 *appsettings.json* 및 *appsettings.{Environment}.json*에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="50e7d-412">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-412">Run the sample app.</span></span> <span data-ttu-id="50e7d-413">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="50e7d-414">표에 표시된 대로 환경에 따라 다른 구성에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="50e7d-415">로깅 구성 키는 콜론(`:`)을 계층 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="50e7d-416">Key</span><span class="sxs-lookup"><span data-stu-id="50e7d-416">Key</span></span>                        | <span data-ttu-id="50e7d-417">개발 값</span><span class="sxs-lookup"><span data-stu-id="50e7d-417">Development Value</span></span> | <span data-ttu-id="50e7d-418">프로덕션 값</span><span class="sxs-lookup"><span data-stu-id="50e7d-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="50e7d-419">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="50e7d-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="50e7d-420">정보</span><span class="sxs-lookup"><span data-stu-id="50e7d-420">Information</span></span>       | <span data-ttu-id="50e7d-421">정보</span><span class="sxs-lookup"><span data-stu-id="50e7d-421">Information</span></span>      |
| <span data-ttu-id="50e7d-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="50e7d-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="50e7d-423">정보</span><span class="sxs-lookup"><span data-stu-id="50e7d-423">Information</span></span>       | <span data-ttu-id="50e7d-424">정보</span><span class="sxs-lookup"><span data-stu-id="50e7d-424">Information</span></span>      |
| <span data-ttu-id="50e7d-425">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="50e7d-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="50e7d-426">Debug</span><span class="sxs-lookup"><span data-stu-id="50e7d-426">Debug</span></span>             | <span data-ttu-id="50e7d-427">Error</span><span class="sxs-lookup"><span data-stu-id="50e7d-427">Error</span></span>            |
| <span data-ttu-id="50e7d-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="50e7d-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="50e7d-429">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-429">XML Configuration Provider</span></span>

<span data-ttu-id="50e7d-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="50e7d-431">XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="50e7d-432">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="50e7d-433">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="50e7d-433">Whether the file is optional.</span></span>
* <span data-ttu-id="50e7d-434">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="50e7d-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="50e7d-435">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="50e7d-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="50e7d-436">구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="50e7d-437">파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="50e7d-438">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="50e7d-439">XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-439">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="50e7d-440">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="50e7d-441">section0:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-441">section0:key0</span></span>
* <span data-ttu-id="50e7d-442">section0:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-442">section0:key1</span></span>
* <span data-ttu-id="50e7d-443">section1:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-443">section1:key0</span></span>
* <span data-ttu-id="50e7d-444">section1:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-444">section1:key1</span></span>

<span data-ttu-id="50e7d-445">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-445">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="50e7d-446">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-446">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="50e7d-447">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-447">section:section0:key:key0</span></span>
* <span data-ttu-id="50e7d-448">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-448">section:section0:key:key1</span></span>
* <span data-ttu-id="50e7d-449">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-449">section:section1:key:key0</span></span>
* <span data-ttu-id="50e7d-450">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-450">section:section1:key:key1</span></span>

<span data-ttu-id="50e7d-451">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-451">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="50e7d-452">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-452">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="50e7d-453">key:attribute</span><span class="sxs-lookup"><span data-stu-id="50e7d-453">key:attribute</span></span>
* <span data-ttu-id="50e7d-454">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="50e7d-454">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="50e7d-455">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-455">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="50e7d-456"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-456">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="50e7d-457">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="50e7d-457">The key is the file name.</span></span> <span data-ttu-id="50e7d-458">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-458">The value contains the file's contents.</span></span> <span data-ttu-id="50e7d-459">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-459">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="50e7d-460">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-460">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="50e7d-461">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-461">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="50e7d-462">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-462">Overloads permit specifying:</span></span>

* <span data-ttu-id="50e7d-463">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="50e7d-463">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="50e7d-464">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="50e7d-464">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="50e7d-465">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-465">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="50e7d-466">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-466">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="50e7d-467">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-467">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="50e7d-468">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-468">Memory Configuration Provider</span></span>

<span data-ttu-id="50e7d-469"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-469">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="50e7d-470">메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-470">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="50e7d-471">`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-471">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="50e7d-472">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-472">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="50e7d-473">다음 예제에서는 구성 사전이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-473">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="50e7d-474">사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-474">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="50e7d-475">GetValue</span><span class="sxs-lookup"><span data-stu-id="50e7d-475">GetValue</span></span>

<span data-ttu-id="50e7d-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 하나의 값을 추출하고 해당 값을 지정된 비컬렉션 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="50e7d-477">오버로드는 기본값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-477">An overload accepts a default value.</span></span>

<span data-ttu-id="50e7d-478">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="50e7d-478">The following example:</span></span>

* <span data-ttu-id="50e7d-479">`NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-479">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="50e7d-480">구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-480">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="50e7d-481">값을 `int` 형식으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-481">Types the value as an `int`.</span></span>
* <span data-ttu-id="50e7d-482">페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-482">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="50e7d-483">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="50e7d-483">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="50e7d-484">이어지는 예제에서는 다음 JSON 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-484">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="50e7d-485">네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-485">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="50e7d-486">파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-486">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="50e7d-487">section0:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-487">section0:key0</span></span>
* <span data-ttu-id="50e7d-488">section0:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-488">section0:key1</span></span>
* <span data-ttu-id="50e7d-489">section1:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-489">section1:key0</span></span>
* <span data-ttu-id="50e7d-490">section1:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-490">section1:key1</span></span>
* <span data-ttu-id="50e7d-491">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-491">section2:subsection0:key0</span></span>
* <span data-ttu-id="50e7d-492">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-492">section2:subsection0:key1</span></span>
* <span data-ttu-id="50e7d-493">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="50e7d-493">section2:subsection1:key0</span></span>
* <span data-ttu-id="50e7d-494">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="50e7d-494">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="50e7d-495">GetSection</span><span class="sxs-lookup"><span data-stu-id="50e7d-495">GetSection</span></span>

<span data-ttu-id="50e7d-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="50e7d-497">`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-497">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="50e7d-498">`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-498">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="50e7d-499">마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-499">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="50e7d-500">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-500">`GetSection` never returns `null`.</span></span> <span data-ttu-id="50e7d-501">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-501">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="50e7d-502">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-502">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="50e7d-503">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-503">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="50e7d-504">GetChildren</span><span class="sxs-lookup"><span data-stu-id="50e7d-504">GetChildren</span></span>

<span data-ttu-id="50e7d-505">`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-505">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="50e7d-506">있음</span><span class="sxs-lookup"><span data-stu-id="50e7d-506">Exists</span></span>

<span data-ttu-id="50e7d-507">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-507">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="50e7d-508">예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-508">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="50e7d-509">클래스에 바인딩</span><span class="sxs-lookup"><span data-stu-id="50e7d-509">Bind to a class</span></span>

<span data-ttu-id="50e7d-510">‘옵션 패턴’을 사용하여 관련 설정 그룹을 나타내는 클래스에 구성을 바인딩할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="50e7d-510">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="50e7d-511">자세한 내용은 <xref:fundamentals/configuration/options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-511">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="50e7d-512">구성 값이 문자열로 반환되지만, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>를 호출하면 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-512">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="50e7d-513">샘플 앱은 `Starship` 모델(*Models/Starship.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-513">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="50e7d-514">샘플 앱이 JSON 구성 공급자를 사용하여 구성을 로드하는 경우 *starship.json* 파일의 `starship` 섹션에서 구성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-514">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="50e7d-515">다음 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-515">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="50e7d-516">Key</span><span class="sxs-lookup"><span data-stu-id="50e7d-516">Key</span></span>                   | <span data-ttu-id="50e7d-517">값</span><span class="sxs-lookup"><span data-stu-id="50e7d-517">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="50e7d-518">starship:name</span><span class="sxs-lookup"><span data-stu-id="50e7d-518">starship:name</span></span>         | <span data-ttu-id="50e7d-519">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="50e7d-519">USS Enterprise</span></span>                                    |
| <span data-ttu-id="50e7d-520">starship:registry</span><span class="sxs-lookup"><span data-stu-id="50e7d-520">starship:registry</span></span>     | <span data-ttu-id="50e7d-521">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="50e7d-521">NCC-1701</span></span>                                          |
| <span data-ttu-id="50e7d-522">starship:class</span><span class="sxs-lookup"><span data-stu-id="50e7d-522">starship:class</span></span>        | <span data-ttu-id="50e7d-523">Constitution</span><span class="sxs-lookup"><span data-stu-id="50e7d-523">Constitution</span></span>                                      |
| <span data-ttu-id="50e7d-524">starship:length</span><span class="sxs-lookup"><span data-stu-id="50e7d-524">starship:length</span></span>       | <span data-ttu-id="50e7d-525">304.8</span><span class="sxs-lookup"><span data-stu-id="50e7d-525">304.8</span></span>                                             |
| <span data-ttu-id="50e7d-526">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="50e7d-526">starship:commissioned</span></span> | <span data-ttu-id="50e7d-527">False</span><span class="sxs-lookup"><span data-stu-id="50e7d-527">False</span></span>                                             |
| <span data-ttu-id="50e7d-528">trademark</span><span class="sxs-lookup"><span data-stu-id="50e7d-528">trademark</span></span>             | <span data-ttu-id="50e7d-529">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="50e7d-529">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="50e7d-530">샘플 앱은 `starship` 키를 사용하여 `GetSection`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-530">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="50e7d-531">`starship` 키-값 쌍은 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-531">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="50e7d-532">`Bind` 메서드는 `Starship` 클래스의 인스턴스를 전달하는 하위 섹션에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-532">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="50e7d-533">인스턴스 값을 바인딩한 후 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-533">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="50e7d-534">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="50e7d-534">Bind to an object graph</span></span>

<span data-ttu-id="50e7d-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="50e7d-536">다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-536">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="50e7d-537">샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-537">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="50e7d-538">`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-538">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="50e7d-539">바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-539">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="50e7d-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="50e7d-541">`Get<T>`가 `Bind`보다 사용하기 더 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-541">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="50e7d-542">다음 코드에서는 앞의 예제에 `Get<T>`를 사용하여 바인딩된 인스턴스가 렌더링에 사용되는 속성에 바로 할당될 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-542">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="50e7d-543">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="50e7d-543">Bind an array to a class</span></span>

<span data-ttu-id="50e7d-544">다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다. </span><span class="sxs-lookup"><span data-stu-id="50e7d-544">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="50e7d-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-545">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="50e7d-546">숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-546">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="50e7d-547">바인딩은 규칙에 따라 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-547">Binding is provided by convention.</span></span> <span data-ttu-id="50e7d-548">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-548">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="50e7d-549">**메모리 내 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="50e7d-549">**In-memory array processing**</span></span>

<span data-ttu-id="50e7d-550">다음 표에 표시된 구성 키 및 값을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-550">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="50e7d-551">Key</span><span class="sxs-lookup"><span data-stu-id="50e7d-551">Key</span></span>             | <span data-ttu-id="50e7d-552">값</span><span class="sxs-lookup"><span data-stu-id="50e7d-552">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="50e7d-553">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="50e7d-553">array:entries:0</span></span> | <span data-ttu-id="50e7d-554">value0</span><span class="sxs-lookup"><span data-stu-id="50e7d-554">value0</span></span> |
| <span data-ttu-id="50e7d-555">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="50e7d-555">array:entries:1</span></span> | <span data-ttu-id="50e7d-556">value1</span><span class="sxs-lookup"><span data-stu-id="50e7d-556">value1</span></span> |
| <span data-ttu-id="50e7d-557">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="50e7d-557">array:entries:2</span></span> | <span data-ttu-id="50e7d-558">value2</span><span class="sxs-lookup"><span data-stu-id="50e7d-558">value2</span></span> |
| <span data-ttu-id="50e7d-559">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="50e7d-559">array:entries:4</span></span> | <span data-ttu-id="50e7d-560">value4</span><span class="sxs-lookup"><span data-stu-id="50e7d-560">value4</span></span> |
| <span data-ttu-id="50e7d-561">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="50e7d-561">array:entries:5</span></span> | <span data-ttu-id="50e7d-562">value5</span><span class="sxs-lookup"><span data-stu-id="50e7d-562">value5</span></span> |

<span data-ttu-id="50e7d-563">메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-563">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="50e7d-564">배열은 인덱스 &num;3에 대한 값을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-564">The array skips a value for index &num;3.</span></span> <span data-ttu-id="50e7d-565">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-565">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="50e7d-566">샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-566">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="50e7d-567">구성 데이터는 개체에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-567">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="50e7d-568">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문도 사용할 수 있으며, 이 경우 코드가 더 간결해집니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-568">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="50e7d-569">바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-569">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="50e7d-570">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="50e7d-570">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="50e7d-571">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="50e7d-571">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="50e7d-572">0</span><span class="sxs-lookup"><span data-stu-id="50e7d-572">0</span></span>                            | <span data-ttu-id="50e7d-573">value0</span><span class="sxs-lookup"><span data-stu-id="50e7d-573">value0</span></span>                       |
| <span data-ttu-id="50e7d-574">1</span><span class="sxs-lookup"><span data-stu-id="50e7d-574">1</span></span>                            | <span data-ttu-id="50e7d-575">value1</span><span class="sxs-lookup"><span data-stu-id="50e7d-575">value1</span></span>                       |
| <span data-ttu-id="50e7d-576">2</span><span class="sxs-lookup"><span data-stu-id="50e7d-576">2</span></span>                            | <span data-ttu-id="50e7d-577">value2</span><span class="sxs-lookup"><span data-stu-id="50e7d-577">value2</span></span>                       |
| <span data-ttu-id="50e7d-578">3</span><span class="sxs-lookup"><span data-stu-id="50e7d-578">3</span></span>                            | <span data-ttu-id="50e7d-579">value4</span><span class="sxs-lookup"><span data-stu-id="50e7d-579">value4</span></span>                       |
| <span data-ttu-id="50e7d-580">4</span><span class="sxs-lookup"><span data-stu-id="50e7d-580">4</span></span>                            | <span data-ttu-id="50e7d-581">value5</span><span class="sxs-lookup"><span data-stu-id="50e7d-581">value5</span></span>                       |

<span data-ttu-id="50e7d-582">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-582">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="50e7d-583">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-583">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="50e7d-584">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-584">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="50e7d-585">인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-585">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="50e7d-586">샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-586">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="50e7d-587">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-587">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="50e7d-588">`ConfigureAppConfiguration`의 경우</span><span class="sxs-lookup"><span data-stu-id="50e7d-588">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="50e7d-589">표에 표시된 키-값 쌍이 구성으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-589">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="50e7d-590">Key</span><span class="sxs-lookup"><span data-stu-id="50e7d-590">Key</span></span>             | <span data-ttu-id="50e7d-591">값</span><span class="sxs-lookup"><span data-stu-id="50e7d-591">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="50e7d-592">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="50e7d-592">array:entries:3</span></span> | <span data-ttu-id="50e7d-593">value3</span><span class="sxs-lookup"><span data-stu-id="50e7d-593">value3</span></span> |

<span data-ttu-id="50e7d-594">JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-594">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="50e7d-595">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="50e7d-595">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="50e7d-596">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="50e7d-596">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="50e7d-597">0</span><span class="sxs-lookup"><span data-stu-id="50e7d-597">0</span></span>                            | <span data-ttu-id="50e7d-598">value0</span><span class="sxs-lookup"><span data-stu-id="50e7d-598">value0</span></span>                       |
| <span data-ttu-id="50e7d-599">1</span><span class="sxs-lookup"><span data-stu-id="50e7d-599">1</span></span>                            | <span data-ttu-id="50e7d-600">value1</span><span class="sxs-lookup"><span data-stu-id="50e7d-600">value1</span></span>                       |
| <span data-ttu-id="50e7d-601">2</span><span class="sxs-lookup"><span data-stu-id="50e7d-601">2</span></span>                            | <span data-ttu-id="50e7d-602">value2</span><span class="sxs-lookup"><span data-stu-id="50e7d-602">value2</span></span>                       |
| <span data-ttu-id="50e7d-603">3</span><span class="sxs-lookup"><span data-stu-id="50e7d-603">3</span></span>                            | <span data-ttu-id="50e7d-604">value3</span><span class="sxs-lookup"><span data-stu-id="50e7d-604">value3</span></span>                       |
| <span data-ttu-id="50e7d-605">4</span><span class="sxs-lookup"><span data-stu-id="50e7d-605">4</span></span>                            | <span data-ttu-id="50e7d-606">value4</span><span class="sxs-lookup"><span data-stu-id="50e7d-606">value4</span></span>                       |
| <span data-ttu-id="50e7d-607">5</span><span class="sxs-lookup"><span data-stu-id="50e7d-607">5</span></span>                            | <span data-ttu-id="50e7d-608">value5</span><span class="sxs-lookup"><span data-stu-id="50e7d-608">value5</span></span>                       |

<span data-ttu-id="50e7d-609">**JSON 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="50e7d-609">**JSON array processing**</span></span>

<span data-ttu-id="50e7d-610">JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-610">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="50e7d-611">다음 구성 파일에서 `subsection`은 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-611">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="50e7d-612">JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-612">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="50e7d-613">Key</span><span class="sxs-lookup"><span data-stu-id="50e7d-613">Key</span></span>                     | <span data-ttu-id="50e7d-614">값</span><span class="sxs-lookup"><span data-stu-id="50e7d-614">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="50e7d-615">json_array:key</span><span class="sxs-lookup"><span data-stu-id="50e7d-615">json_array:key</span></span>          | <span data-ttu-id="50e7d-616">valueA</span><span class="sxs-lookup"><span data-stu-id="50e7d-616">valueA</span></span> |
| <span data-ttu-id="50e7d-617">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="50e7d-617">json_array:subsection:0</span></span> | <span data-ttu-id="50e7d-618">valueB</span><span class="sxs-lookup"><span data-stu-id="50e7d-618">valueB</span></span> |
| <span data-ttu-id="50e7d-619">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="50e7d-619">json_array:subsection:1</span></span> | <span data-ttu-id="50e7d-620">valueC</span><span class="sxs-lookup"><span data-stu-id="50e7d-620">valueC</span></span> |
| <span data-ttu-id="50e7d-621">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="50e7d-621">json_array:subsection:2</span></span> | <span data-ttu-id="50e7d-622">valueD</span><span class="sxs-lookup"><span data-stu-id="50e7d-622">valueD</span></span> |

<span data-ttu-id="50e7d-623">샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-623">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="50e7d-624">바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-624">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="50e7d-625">하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-625">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="50e7d-626">`JsonArrayExample.Subsection` 인덱스</span><span class="sxs-lookup"><span data-stu-id="50e7d-626">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="50e7d-627">`JsonArrayExample.Subsection` 값</span><span class="sxs-lookup"><span data-stu-id="50e7d-627">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="50e7d-628">0</span><span class="sxs-lookup"><span data-stu-id="50e7d-628">0</span></span>                                   | <span data-ttu-id="50e7d-629">valueB</span><span class="sxs-lookup"><span data-stu-id="50e7d-629">valueB</span></span>                              |
| <span data-ttu-id="50e7d-630">1</span><span class="sxs-lookup"><span data-stu-id="50e7d-630">1</span></span>                                   | <span data-ttu-id="50e7d-631">valueC</span><span class="sxs-lookup"><span data-stu-id="50e7d-631">valueC</span></span>                              |
| <span data-ttu-id="50e7d-632">2</span><span class="sxs-lookup"><span data-stu-id="50e7d-632">2</span></span>                                   | <span data-ttu-id="50e7d-633">valueD</span><span class="sxs-lookup"><span data-stu-id="50e7d-633">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="50e7d-634">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="50e7d-634">Custom configuration provider</span></span>

<span data-ttu-id="50e7d-635">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-635">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="50e7d-636">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-636">The provider has the following characteristics:</span></span>

* <span data-ttu-id="50e7d-637">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-637">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="50e7d-638">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-638">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="50e7d-639">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-639">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="50e7d-640">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-640">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="50e7d-641">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-641">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="50e7d-642">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-642">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="50e7d-643">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-643">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="50e7d-644">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-644">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="50e7d-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="50e7d-646"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-646">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="50e7d-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="50e7d-648"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-648">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="50e7d-649">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-649">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="50e7d-650">[구성 키는 대/소문자를](#keys)구분하지 않으므로 데이터베이스를 초기화하는 데 사용되는 사전은 대/소문자를 구분하지 않는 비교자([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase))를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-650">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="50e7d-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="50e7d-652">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-652">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="50e7d-653">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-653">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="50e7d-654">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-654">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50e7d-655">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-655">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="50e7d-656">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-656">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="50e7d-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="50e7d-658"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-658">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="50e7d-659">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-659">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="50e7d-660"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-660">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="50e7d-661">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-661">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="50e7d-662">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-662">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="50e7d-663">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-663">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="50e7d-664">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="50e7d-664">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="50e7d-665">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-665">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="50e7d-666">시작하는 동안 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="50e7d-666">Access configuration during startup</span></span>

<span data-ttu-id="50e7d-667">`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-667">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="50e7d-668">`Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-668">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="50e7d-669">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-669">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="50e7d-670">Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="50e7d-670">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="50e7d-671">Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-671">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="50e7d-672">Razor 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="50e7d-672">In a Razor Pages page:</span></span>

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

<span data-ttu-id="50e7d-673">MVC 뷰에서:</span><span class="sxs-lookup"><span data-stu-id="50e7d-673">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="50e7d-674">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="50e7d-674">Add configuration from an external assembly</span></span>

<span data-ttu-id="50e7d-675"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="50e7d-675">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="50e7d-676">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="50e7d-676">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50e7d-677">추가 자료</span><span class="sxs-lookup"><span data-stu-id="50e7d-677">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
