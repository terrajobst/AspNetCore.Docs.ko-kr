---
title: ASP.NET Core의 구성
author: guardrex
description: 구성 API를 사용하여 ASP.NET Core 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/24/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 3f7588f9ba18e300f5947e8bb0daf2e72d580a94
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223161"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="7ee2f-103">ASP.NET Core의 구성</span><span class="sxs-lookup"><span data-stu-id="7ee2f-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="7ee2f-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="7ee2f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ee2f-105">ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다. </span><span class="sxs-lookup"><span data-stu-id="7ee2f-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="7ee2f-106">구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="7ee2f-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7ee2f-107">Azure Key Vault</span></span>
* <span data-ttu-id="7ee2f-108">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-108">Command-line arguments</span></span>
* <span data-ttu-id="7ee2f-109">사용자 지정 공급자(설치 또는 생성된)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="7ee2f-110">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="7ee2f-110">Directory files</span></span>
* <span data-ttu-id="7ee2f-111">환경 변수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-111">Environment variables</span></span>
* <span data-ttu-id="7ee2f-112">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="7ee2f-112">In-memory .NET objects</span></span>
* <span data-ttu-id="7ee2f-113">설정 파일</span><span class="sxs-lookup"><span data-stu-id="7ee2f-113">Settings files</span></span>

<span data-ttu-id="7ee2f-114">공통 구성 공급자 시나리오용 구성 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-114">Configuration packages for common configuration provider scenarios are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="7ee2f-115">샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-115">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="7ee2f-116">‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다. </span><span class="sxs-lookup"><span data-stu-id="7ee2f-116">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="7ee2f-117">옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-117">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="7ee2f-118">옵션 패턴 사용에 대한 자세한 내용은 <xref:fundamentals/configuration/options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-118">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="7ee2f-119">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ee2f-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-vs-app-configuration"></a><span data-ttu-id="7ee2f-120">호스트 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7ee2f-120">Host vs. app configuration</span></span>

<span data-ttu-id="7ee2f-121">앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-121">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="7ee2f-122">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-122">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="7ee2f-123">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-123">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="7ee2f-124">호스트 구성 키-값 쌍은 앱의 전역 구성에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-124">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="7ee2f-125">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 원본이 호스트 구성에 미치는 영향에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-125">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="7ee2f-126">기본 구성</span><span class="sxs-lookup"><span data-stu-id="7ee2f-126">Default configuration</span></span>

<span data-ttu-id="7ee2f-127">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-127">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="7ee2f-128"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-128"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="7ee2f-129">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-129">Host configuration is provided from:</span></span>
  * <span data-ttu-id="7ee2f-130">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `ASPNETCORE_`를 접두사로 사용하는 환경 변수(예: `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-130">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="7ee2f-131">구성 키-값 쌍이 로드되면 접두사(`ASPNETCORE_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-131">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="7ee2f-132">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-132">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="7ee2f-133">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-133">App configuration is provided from:</span></span>
  * <span data-ttu-id="7ee2f-134">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="7ee2f-134">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="7ee2f-135">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="7ee2f-135">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="7ee2f-136">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="7ee2f-137">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-137">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="7ee2f-138">사용자 지정 접두사가 사용되는 경우(예: `.AddEnvironmentVariables(prefix: "PREFIX_")`가 포함된 `PREFIX_`), 구성 키-값 쌍이 로드되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-138">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="7ee2f-139">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-139">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="7ee2f-140">구성 공급자에 대해서는 이 항목의 뒷부분에서 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-140">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="7ee2f-141">호스트 및 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 대한 추가 정보는 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-141">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="7ee2f-142">보안</span><span class="sxs-lookup"><span data-stu-id="7ee2f-142">Security</span></span>

<span data-ttu-id="7ee2f-143">다음 모범 사례를 따르세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-143">Adopt the following best practices:</span></span>

* <span data-ttu-id="7ee2f-144">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-144">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="7ee2f-145">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-145">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="7ee2f-146">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-146">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="7ee2f-147">[여러 환경을 사용하는 방법](xref:fundamentals/environments)과 [개발 시 비밀 관리자를 사용해 앱 비밀을 안전하게 보관](xref:security/app-secrets)하여 관리하는 방법(환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언 포함)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-147">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="7ee2f-148">비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-148">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="7ee2f-149">파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-149">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="7ee2f-150">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)는 앱 비밀을 안전하게 보관하기 위한 한 가지 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-150">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="7ee2f-151">자세한 내용은 <xref:security/key-vault-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-151">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="7ee2f-152">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="7ee2f-152">Hierarchical configuration data</span></span>

<span data-ttu-id="7ee2f-153">구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-153">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="7ee2f-154">다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-154">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="7ee2f-155">파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-155">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="7ee2f-156">콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-156">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="7ee2f-157">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-157">section0:key0</span></span>
* <span data-ttu-id="7ee2f-158">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-158">section0:key1</span></span>
* <span data-ttu-id="7ee2f-159">section1:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-159">section1:key0</span></span>
* <span data-ttu-id="7ee2f-160">section1:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-160">section1:key1</span></span>

<span data-ttu-id="7ee2f-161">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-161"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="7ee2f-162">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-162">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="7ee2f-163">`GetSection`은 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-163">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="7ee2f-164">규칙</span><span class="sxs-lookup"><span data-stu-id="7ee2f-164">Conventions</span></span>

<span data-ttu-id="7ee2f-165">앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-165">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="7ee2f-166">변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-166">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="7ee2f-167">예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-167">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="7ee2f-168"><xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7ee2f-169"><xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-169"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

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

<span data-ttu-id="7ee2f-170">구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-170">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="7ee2f-171">구성키는 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-171">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="7ee2f-172">키는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-172">Keys are case-insensitive.</span></span> <span data-ttu-id="7ee2f-173">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-173">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="7ee2f-174">같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-174">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="7ee2f-175">계층적 키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-175">Hierarchical keys</span></span>
  * <span data-ttu-id="7ee2f-176">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-176">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="7ee2f-177">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-177">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="7ee2f-178">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-178">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="7ee2f-179">Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-179">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="7ee2f-180">비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-180">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="7ee2f-181"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-181">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="7ee2f-182">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-182">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="7ee2f-183">구성 값은 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-183">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="7ee2f-184">값은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-184">Values are strings.</span></span>
* <span data-ttu-id="7ee2f-185">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-185">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="7ee2f-186">공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-186">Providers</span></span>

<span data-ttu-id="7ee2f-187">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-187">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="7ee2f-188">공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-188">Provider</span></span> | <span data-ttu-id="7ee2f-189">다음에서 구성 제공&hellip;</span><span class="sxs-lookup"><span data-stu-id="7ee2f-189">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="7ee2f-190">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="7ee2f-190">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="7ee2f-191">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7ee2f-191">Azure Key Vault</span></span> |
| [<span data-ttu-id="7ee2f-192">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-192">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="7ee2f-193">명령줄 매개 변수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-193">Command-line parameters</span></span> |
| [<span data-ttu-id="7ee2f-194">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-194">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="7ee2f-195">사용자 지정 소스</span><span class="sxs-lookup"><span data-stu-id="7ee2f-195">Custom source</span></span> |
| [<span data-ttu-id="7ee2f-196">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-196">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="7ee2f-197">환경 변수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-197">Environment variables</span></span> |
| [<span data-ttu-id="7ee2f-198">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-198">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="7ee2f-199">파일(INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-199">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="7ee2f-200">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-200">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="7ee2f-201">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="7ee2f-201">Directory files</span></span> |
| [<span data-ttu-id="7ee2f-202">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-202">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="7ee2f-203">메모리 내 컬렉션</span><span class="sxs-lookup"><span data-stu-id="7ee2f-203">In-memory collections</span></span> |
| <span data-ttu-id="7ee2f-204">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목) </span><span class="sxs-lookup"><span data-stu-id="7ee2f-204">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="7ee2f-205">사용자 프로필 디렉터리의 파일</span><span class="sxs-lookup"><span data-stu-id="7ee2f-205">File in the user profile directory</span></span> |

<span data-ttu-id="7ee2f-206">시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-206">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="7ee2f-207">이 항목의 구성 공급자는 코드에서 정렬할 수 있는 순서가 아니라 사전순으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-207">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="7ee2f-208">코드에서는 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-208">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="7ee2f-209">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-209">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="7ee2f-210">파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-210">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="7ee2f-211">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7ee2f-211">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="7ee2f-212">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에만 해당)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-212">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="7ee2f-213">환경 변수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-213">Environment variables</span></span>
1. <span data-ttu-id="7ee2f-214">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-214">Command-line arguments</span></span>

<span data-ttu-id="7ee2f-215">일반적으로 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하며, 따라서 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-215">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="7ee2f-216">이 공급자 순서는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 사용하여 새 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>를 초기화할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-216">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="7ee2f-217">자세한 내용은 [웹 호스트: 호스트 설정 편을](xref:fundamentals/host/web-host#set-up-a-host) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-217">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="7ee2f-218">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="7ee2f-218">ConfigureAppConfiguration</span></span>

<span data-ttu-id="7ee2f-219">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-219">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

<span data-ttu-id="7ee2f-220">`Startup.ConfigureServices`를 포함하여 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-220">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7ee2f-221">자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-221">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="7ee2f-222">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-222">Command-line Configuration Provider</span></span>

<span data-ttu-id="7ee2f-223"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-223">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="7ee2f-224">명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-224">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ee2f-225">`AddCommandLine`는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 사용하여 새 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>를 초기화할 때 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-225">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="7ee2f-226">자세한 내용은 [웹 호스트: 호스트 설정 편을](xref:fundamentals/host/web-host#set-up-a-host) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-226">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="7ee2f-227">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-227">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="7ee2f-228">*appsettings.json* 및 *appsettings.{Environment}.json*에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="7ee2f-228">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="7ee2f-229">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-229">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="7ee2f-230">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-230">Environment variables.</span></span>

<span data-ttu-id="7ee2f-231">`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-231">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="7ee2f-232">따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-232">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="7ee2f-233">`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-233">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="7ee2f-234">따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-234">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="7ee2f-235">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-235">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="7ee2f-236">`CreateDefaultBuilder`가 이미 `AddCommandLine`을 호출했습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-236">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7ee2f-237">앱 구성을 제공해야 하며, 명령줄 인수로 해당 구성을 재정의할 수 있는 경우 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에 제공된 앱의 추가 제공 업체에 문의하고 마지막에 `AddCommandLine`을 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-237">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-238"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>을 직접 만들 경우 다음 구성으로 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-238">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="7ee2f-239">**예제**</span><span class="sxs-lookup"><span data-stu-id="7ee2f-239">**Example**</span></span>

<span data-ttu-id="7ee2f-240">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-240">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="7ee2f-241">프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-241">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="7ee2f-242">`dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).</span><span class="sxs-lookup"><span data-stu-id="7ee2f-242">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="7ee2f-243">앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-243">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="7ee2f-244">`dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-244">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="7ee2f-245">인수</span><span class="sxs-lookup"><span data-stu-id="7ee2f-245">Arguments</span></span>

<span data-ttu-id="7ee2f-246">값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-246">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="7ee2f-247">등호를 사용하는 경우 값이 null일 수 있습니다(예: `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="7ee2f-247">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="7ee2f-248">키 접두사</span><span class="sxs-lookup"><span data-stu-id="7ee2f-248">Key prefix</span></span>               | <span data-ttu-id="7ee2f-249">예제</span><span class="sxs-lookup"><span data-stu-id="7ee2f-249">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="7ee2f-250">접두사 없음</span><span class="sxs-lookup"><span data-stu-id="7ee2f-250">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="7ee2f-251">대시 2개(`--`)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-251">Two dashes (`--`)</span></span>        | <span data-ttu-id="7ee2f-252">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="7ee2f-252">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="7ee2f-253">슬래시(`/`)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-253">Forward slash (`/`)</span></span>      | <span data-ttu-id="7ee2f-254">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="7ee2f-254">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="7ee2f-255">같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-255">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="7ee2f-256">명령 예:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-256">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="7ee2f-257">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="7ee2f-257">Switch mappings</span></span>

<span data-ttu-id="7ee2f-258">스위치 매핑은 키 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-258">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="7ee2f-259"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-259">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="7ee2f-260">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-260">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="7ee2f-261">사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-261">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="7ee2f-262">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-262">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="7ee2f-263">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-263">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="7ee2f-264">스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-264">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="7ee2f-265">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-265">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="7ee2f-266">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-266">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-267">앞의 예제와 같이 스위치 매핑이 사용되는 경우 `CreateDefaultBuilder` 호출에서 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-267">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="7ee2f-268">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-268">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="7ee2f-269">인수가 매핑된 스위치를 포함하고 `CreateDefaultBuilder`에 전달되는 경우 해당 `AddCommandLine` 공급자는 <xref:System.FormatException>으로 초기화하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-269">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="7ee2f-270">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-270">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="7ee2f-271">생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-271">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="7ee2f-272">키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-272">Key</span></span>       | <span data-ttu-id="7ee2f-273">값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-273">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="7ee2f-274">앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-274">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="7ee2f-275">앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-275">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="7ee2f-276">키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-276">Key</span></span>               | <span data-ttu-id="7ee2f-277">값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-277">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="7ee2f-278">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-278">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="7ee2f-279"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-279">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="7ee2f-280">환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-280">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="7ee2f-281">[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-281">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="7ee2f-282">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-282">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="7ee2f-283">새 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>를 초기화할 때 `ASPNETCORE_`가 접두사인 환경 변수의 경우 `AddEnvironmentVariables`가 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-283">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="7ee2f-284">자세한 내용은 [웹 호스트: 호스트 설정 편을](xref:fundamentals/host/web-host#set-up-a-host) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-284">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="7ee2f-285">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-285">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="7ee2f-286">접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7ee2f-286">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="7ee2f-287">*appsettings.json* 및 *appsettings.{Environment}.json*에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="7ee2f-287">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="7ee2f-288">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-288">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="7ee2f-289">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-289">Command-line arguments.</span></span>

<span data-ttu-id="7ee2f-290">사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-290">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="7ee2f-291">이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-291">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="7ee2f-292">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-292">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="7ee2f-293">추가 환경 변수에서 앱 구성을 제공해야 하는 경우에는 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-293">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow
                // environment variables to override values from other 
                // providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-294"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>을 직접 만들 경우 다음 구성으로 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-294">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="7ee2f-295">**예제**</span><span class="sxs-lookup"><span data-stu-id="7ee2f-295">**Example**</span></span>

<span data-ttu-id="7ee2f-296">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-296">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="7ee2f-297">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-297">Run the sample app.</span></span> <span data-ttu-id="7ee2f-298">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-298">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="7ee2f-299">환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-299">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="7ee2f-300">이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-300">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="7ee2f-301">앱에서 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 앱에서는 다음으로 시작하는 변수로 환경 변수를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-301">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="7ee2f-302">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="7ee2f-302">ASPNETCORE_</span></span>
* <span data-ttu-id="7ee2f-303">urls</span><span class="sxs-lookup"><span data-stu-id="7ee2f-303">urls</span></span>
* <span data-ttu-id="7ee2f-304">로깅</span><span class="sxs-lookup"><span data-stu-id="7ee2f-304">Logging</span></span>
* <span data-ttu-id="7ee2f-305">ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="7ee2f-305">ENVIRONMENT</span></span>
* <span data-ttu-id="7ee2f-306">contentRoot</span><span class="sxs-lookup"><span data-stu-id="7ee2f-306">contentRoot</span></span>
* <span data-ttu-id="7ee2f-307">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="7ee2f-307">AllowedHosts</span></span>
* <span data-ttu-id="7ee2f-308">applicationName</span><span class="sxs-lookup"><span data-stu-id="7ee2f-308">applicationName</span></span>
* <span data-ttu-id="7ee2f-309">CommandLine</span><span class="sxs-lookup"><span data-stu-id="7ee2f-309">CommandLine</span></span>

<span data-ttu-id="7ee2f-310">앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-310">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="7ee2f-311">접두사</span><span class="sxs-lookup"><span data-stu-id="7ee2f-311">Prefixes</span></span>

<span data-ttu-id="7ee2f-312">`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-312">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="7ee2f-313">예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-313">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="7ee2f-314">구성 키-값 쌍이 생성되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-314">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="7ee2f-315">정적 편의 메서드 `CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>를 생성하여 앱의 호스트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-315">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="7ee2f-316">생성된 `WebHostBuilder`는 앞에 `ASPNETCORE_`가 붙은 환경 변수에서 호스트 구성을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-316">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="7ee2f-317">**연결 문자열 접두사**</span><span class="sxs-lookup"><span data-stu-id="7ee2f-317">**Connection string prefixes**</span></span>

<span data-ttu-id="7ee2f-318">구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-318">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="7ee2f-319">즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-319">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="7ee2f-320">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="7ee2f-320">Connection string prefix</span></span> | <span data-ttu-id="7ee2f-321">공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-321">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="7ee2f-322">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-322">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="7ee2f-323">MySQL</span><span class="sxs-lookup"><span data-stu-id="7ee2f-323">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="7ee2f-324">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="7ee2f-324">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="7ee2f-325">SQL Server</span><span class="sxs-lookup"><span data-stu-id="7ee2f-325">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="7ee2f-326">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-326">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="7ee2f-327">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-327">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="7ee2f-328">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-328">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="7ee2f-329">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-329">Environment variable key</span></span> | <span data-ttu-id="7ee2f-330">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-330">Converted configuration key</span></span> | <span data-ttu-id="7ee2f-331">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="7ee2f-331">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="7ee2f-332">구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-332">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="7ee2f-333">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-333">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="7ee2f-334">값: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ee2f-334">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="7ee2f-335">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-335">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="7ee2f-336">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ee2f-336">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="7ee2f-337">키: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-337">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="7ee2f-338">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="7ee2f-338">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="7ee2f-339">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-339">File Configuration Provider</span></span>

<span data-ttu-id="7ee2f-340"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-340"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="7ee2f-341">다음 구성 공급자는 특정 파일 형식 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-341">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="7ee2f-342">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-342">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="7ee2f-343">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-343">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="7ee2f-344">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-344">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="7ee2f-345">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-345">INI Configuration Provider</span></span>

<span data-ttu-id="7ee2f-346"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-346">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="7ee2f-347">INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-347">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ee2f-348">콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-348">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="7ee2f-349">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-349">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ee2f-350">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="7ee2f-350">Whether the file is optional.</span></span>
* <span data-ttu-id="7ee2f-351">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="7ee2f-351">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="7ee2f-352">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="7ee2f-352">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="7ee2f-353">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-353">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile(
                    "config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-354">기본 경로는 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-354">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7ee2f-355">`SetBasePath`는 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-355">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-356"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>을 직접 만들 경우 다음 구성으로 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-356">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="7ee2f-357">기본 경로는 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-357">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7ee2f-358">`SetBasePath`는 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-358">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-359">INI 구성 파일의 일반적인 예:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-359">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="7ee2f-360">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-360">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ee2f-361">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-361">section0:key0</span></span>
* <span data-ttu-id="7ee2f-362">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-362">section0:key1</span></span>
* <span data-ttu-id="7ee2f-363">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="7ee2f-363">section1:subsection:key</span></span>
* <span data-ttu-id="7ee2f-364">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="7ee2f-364">section2:subsection0:key</span></span>
* <span data-ttu-id="7ee2f-365">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="7ee2f-365">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="7ee2f-366">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-366">JSON Configuration Provider</span></span>

<span data-ttu-id="7ee2f-367"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-367">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="7ee2f-368">JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-368">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ee2f-369">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-369">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ee2f-370">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="7ee2f-370">Whether the file is optional.</span></span>
* <span data-ttu-id="7ee2f-371">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="7ee2f-371">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="7ee2f-372">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="7ee2f-372">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="7ee2f-373">`AddJsonFile`은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>을 사용하여 새 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>를 초기화할 때 자동으로 두 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-373">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="7ee2f-374">이 메서드는 호출되면 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-374">The method is called to load configuration from:</span></span>

* <span data-ttu-id="7ee2f-375">*appsettings.json* &ndash; 이 파일을 먼저 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-375">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="7ee2f-376">파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-376">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="7ee2f-377">*appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-377">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="7ee2f-378">자세한 내용은 [웹 호스트: 호스트 설정 편을](xref:fundamentals/host/web-host#set-up-a-host) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-378">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="7ee2f-379">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-379">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="7ee2f-380">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-380">Environment variables.</span></span>
* <span data-ttu-id="7ee2f-381">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-381">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="7ee2f-382">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-382">Command-line arguments.</span></span>

<span data-ttu-id="7ee2f-383">JSON 구성 공급자를 먼저 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-383">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="7ee2f-384">따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-384">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="7ee2f-385">*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-385">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile(
                    "config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-386">기본 경로는 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-386">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7ee2f-387">`SetBasePath`는 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-387">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-388"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>을 직접 만들 경우 다음 구성으로 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-388">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="7ee2f-389">기본 경로는 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-389">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7ee2f-390">`SetBasePath`는 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-390">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-391">**예제**</span><span class="sxs-lookup"><span data-stu-id="7ee2f-391">**Example**</span></span>

<span data-ttu-id="7ee2f-392">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-392">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="7ee2f-393">구성은 *appsettings.json* 및 *appsettings.{Environment}.json*에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-393">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="7ee2f-394">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-394">Run the sample app.</span></span> <span data-ttu-id="7ee2f-395">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-395">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="7ee2f-396">표에 표시된 대로 환경에 따라 다른 구성에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-396">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="7ee2f-397">로깅 구성 키는 콜론(`:`)을 계층 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-397">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="7ee2f-398">키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-398">Key</span></span>                        | <span data-ttu-id="7ee2f-399">개발 값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-399">Development Value</span></span> | <span data-ttu-id="7ee2f-400">프로덕션 값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-400">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="7ee2f-401">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="7ee2f-401">Logging:LogLevel:System</span></span>    | <span data-ttu-id="7ee2f-402">정보</span><span class="sxs-lookup"><span data-stu-id="7ee2f-402">Information</span></span>       | <span data-ttu-id="7ee2f-403">정보</span><span class="sxs-lookup"><span data-stu-id="7ee2f-403">Information</span></span>      |
| <span data-ttu-id="7ee2f-404">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="7ee2f-404">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="7ee2f-405">정보</span><span class="sxs-lookup"><span data-stu-id="7ee2f-405">Information</span></span>       | <span data-ttu-id="7ee2f-406">정보</span><span class="sxs-lookup"><span data-stu-id="7ee2f-406">Information</span></span>      |
| <span data-ttu-id="7ee2f-407">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="7ee2f-407">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="7ee2f-408">디버그</span><span class="sxs-lookup"><span data-stu-id="7ee2f-408">Debug</span></span>             | <span data-ttu-id="7ee2f-409">오류</span><span class="sxs-lookup"><span data-stu-id="7ee2f-409">Error</span></span>            |
| <span data-ttu-id="7ee2f-410">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="7ee2f-410">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="7ee2f-411">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-411">XML Configuration Provider</span></span>

<span data-ttu-id="7ee2f-412"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-412">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="7ee2f-413">XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-413">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ee2f-414">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-414">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ee2f-415">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="7ee2f-415">Whether the file is optional.</span></span>
* <span data-ttu-id="7ee2f-416">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="7ee2f-416">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="7ee2f-417">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="7ee2f-417">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="7ee2f-418">구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-418">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="7ee2f-419">파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-419">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="7ee2f-420">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-420">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile(
                    "config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-421">기본 경로는 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-421">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7ee2f-422">`SetBasePath`는 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-422">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-423"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>을 직접 만들 경우 다음 구성으로 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-423">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="7ee2f-424">기본 경로는 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-424">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7ee2f-425">`SetBasePath`는 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-425">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-426">XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-426">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="7ee2f-427">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-427">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ee2f-428">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-428">section0:key0</span></span>
* <span data-ttu-id="7ee2f-429">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-429">section0:key1</span></span>
* <span data-ttu-id="7ee2f-430">section1:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-430">section1:key0</span></span>
* <span data-ttu-id="7ee2f-431">section1:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-431">section1:key1</span></span>

<span data-ttu-id="7ee2f-432">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-432">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="7ee2f-433">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-433">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ee2f-434">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-434">section:section0:key:key0</span></span>
* <span data-ttu-id="7ee2f-435">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-435">section:section0:key:key1</span></span>
* <span data-ttu-id="7ee2f-436">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-436">section:section1:key:key0</span></span>
* <span data-ttu-id="7ee2f-437">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-437">section:section1:key:key1</span></span>

<span data-ttu-id="7ee2f-438">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-438">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="7ee2f-439">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-439">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="7ee2f-440">key:attribute</span><span class="sxs-lookup"><span data-stu-id="7ee2f-440">key:attribute</span></span>
* <span data-ttu-id="7ee2f-441">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="7ee2f-441">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="7ee2f-442">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-442">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="7ee2f-443"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-443">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="7ee2f-444">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="7ee2f-444">The key is the file name.</span></span> <span data-ttu-id="7ee2f-445">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-445">The value contains the file's contents.</span></span> <span data-ttu-id="7ee2f-446">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-446">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="7ee2f-447">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-447">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="7ee2f-448">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-448">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="7ee2f-449">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-449">Overloads permit specifying:</span></span>

* <span data-ttu-id="7ee2f-450">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-450">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="7ee2f-451">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="7ee2f-451">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="7ee2f-452">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-452">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="7ee2f-453">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-453">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="7ee2f-454">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-454">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(
                    Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-455">기본 경로는 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-455">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="7ee2f-456">`SetBasePath`는 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-456">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-457"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>을 직접 만들 경우 다음 구성으로 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-457">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="7ee2f-458">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-458">Memory Configuration Provider</span></span>

<span data-ttu-id="7ee2f-459"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-459">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="7ee2f-460">메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-460">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="7ee2f-461">`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-461">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="7ee2f-462">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-462">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="7ee2f-463"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>을 직접 만들 경우 다음 구성으로 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-463">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a><span data-ttu-id="7ee2f-464">GetValue</span><span class="sxs-lookup"><span data-stu-id="7ee2f-464">GetValue</span></span>

<span data-ttu-id="7ee2f-465">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 값을 추출하고 이 값을 지정된 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-465">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="7ee2f-466">오버로드를 사용하면 키를 찾을 수 없는 경우 기본값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-466">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="7ee2f-467">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-467">The following example:</span></span>

* <span data-ttu-id="7ee2f-468">`NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-468">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="7ee2f-469">구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-469">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="7ee2f-470">값을 `int` 형식으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-470">Types the value as an `int`.</span></span>
* <span data-ttu-id="7ee2f-471">페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-471">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="7ee2f-472">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="7ee2f-472">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="7ee2f-473">이어지는 예제에서는 다음 JSON 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-473">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="7ee2f-474">네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-474">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="7ee2f-475">파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-475">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="7ee2f-476">section0:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-476">section0:key0</span></span>
* <span data-ttu-id="7ee2f-477">section0:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-477">section0:key1</span></span>
* <span data-ttu-id="7ee2f-478">section1:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-478">section1:key0</span></span>
* <span data-ttu-id="7ee2f-479">section1:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-479">section1:key1</span></span>
* <span data-ttu-id="7ee2f-480">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-480">section2:subsection0:key0</span></span>
* <span data-ttu-id="7ee2f-481">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-481">section2:subsection0:key1</span></span>
* <span data-ttu-id="7ee2f-482">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-482">section2:subsection1:key0</span></span>
* <span data-ttu-id="7ee2f-483">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-483">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="7ee2f-484">GetSection</span><span class="sxs-lookup"><span data-stu-id="7ee2f-484">GetSection</span></span>

<span data-ttu-id="7ee2f-485">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-485">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="7ee2f-486">`GetSection`은 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-486">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-487">`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-487">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="7ee2f-488">`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-488">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="7ee2f-489">마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-489">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="7ee2f-490">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-490">`GetSection` never returns `null`.</span></span> <span data-ttu-id="7ee2f-491">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-491">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="7ee2f-492">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-492">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="7ee2f-493">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-493">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="7ee2f-494">GetChildren</span><span class="sxs-lookup"><span data-stu-id="7ee2f-494">GetChildren</span></span>

<span data-ttu-id="7ee2f-495">`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-495">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="7ee2f-496">있음</span><span class="sxs-lookup"><span data-stu-id="7ee2f-496">Exists</span></span>

<span data-ttu-id="7ee2f-497">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-497">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="7ee2f-498">예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-498">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="7ee2f-499">클래스에 바인딩</span><span class="sxs-lookup"><span data-stu-id="7ee2f-499">Bind to a class</span></span>

<span data-ttu-id="7ee2f-500">‘옵션 패턴’을 사용하여 관련 설정 그룹을 나타내는 클래스에 구성을 바인딩할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="7ee2f-500">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="7ee2f-501">자세한 내용은 <xref:fundamentals/configuration/options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="7ee2f-502">구성 값이 문자열로 반환되지만, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>를 호출하면 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-502">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="7ee2f-503">`Bind`은 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-503">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-504">샘플 앱은 `Starship` 모델(*Models/Starship.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-504">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="7ee2f-505">샘플 앱이 JSON 구성 공급자를 사용하여 구성을 로드하는 경우 *starship.json* 파일의 `starship` 섹션에서 구성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-505">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="7ee2f-506">다음 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-506">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="7ee2f-507">키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-507">Key</span></span>                   | <span data-ttu-id="7ee2f-508">값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-508">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="7ee2f-509">starship:name</span><span class="sxs-lookup"><span data-stu-id="7ee2f-509">starship:name</span></span>         | <span data-ttu-id="7ee2f-510">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="7ee2f-510">USS Enterprise</span></span>                                    |
| <span data-ttu-id="7ee2f-511">starship:registry</span><span class="sxs-lookup"><span data-stu-id="7ee2f-511">starship:registry</span></span>     | <span data-ttu-id="7ee2f-512">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="7ee2f-512">NCC-1701</span></span>                                          |
| <span data-ttu-id="7ee2f-513">starship:class</span><span class="sxs-lookup"><span data-stu-id="7ee2f-513">starship:class</span></span>        | <span data-ttu-id="7ee2f-514">Constitution</span><span class="sxs-lookup"><span data-stu-id="7ee2f-514">Constitution</span></span>                                      |
| <span data-ttu-id="7ee2f-515">starship:length</span><span class="sxs-lookup"><span data-stu-id="7ee2f-515">starship:length</span></span>       | <span data-ttu-id="7ee2f-516">304.8</span><span class="sxs-lookup"><span data-stu-id="7ee2f-516">304.8</span></span>                                             |
| <span data-ttu-id="7ee2f-517">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="7ee2f-517">starship:commissioned</span></span> | <span data-ttu-id="7ee2f-518">False</span><span class="sxs-lookup"><span data-stu-id="7ee2f-518">False</span></span>                                             |
| <span data-ttu-id="7ee2f-519">trademark</span><span class="sxs-lookup"><span data-stu-id="7ee2f-519">trademark</span></span>             | <span data-ttu-id="7ee2f-520">Paramount Pictures Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="7ee2f-520">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="7ee2f-521">샘플 앱은 `starship` 키를 사용하여 `GetSection`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-521">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="7ee2f-522">`starship` 키-값 쌍은 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-522">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="7ee2f-523">`Bind` 메서드는 `Starship` 클래스의 인스턴스를 전달하는 하위 섹션에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-523">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="7ee2f-524">인스턴스 값을 바인딩한 후 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-524">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="7ee2f-525">`GetSection`은 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-525">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="7ee2f-526">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="7ee2f-526">Bind to an object graph</span></span>

<span data-ttu-id="7ee2f-527"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-527"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="7ee2f-528">`Bind`은 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-528">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-529">다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-529">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="7ee2f-530">샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-530">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="7ee2f-531">`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-531">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="7ee2f-532">바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-532">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="7ee2f-533">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-533">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="7ee2f-534">`Get<T>`가 `Bind`보다 사용하기 더 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-534">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="7ee2f-535">다음 코드에서는 앞의 예제에 `Get<T>`를 사용하여 바인딩된 인스턴스가 렌더링에 사용되는 속성에 바로 할당될 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-535">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="7ee2f-536"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*>은 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-536"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="7ee2f-537">`Get<T>`는 ASP.NET Core 1.1 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-537">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7ee2f-538">`GetSection`은 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-538">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="7ee2f-539">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="7ee2f-539">Bind an array to a class</span></span>

<span data-ttu-id="7ee2f-540">다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다. </span><span class="sxs-lookup"><span data-stu-id="7ee2f-540">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="7ee2f-541"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-541">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="7ee2f-542">숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-542">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="7ee2f-543">\`Bind\`\`는 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-543">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="7ee2f-544">바인딩은 규칙에 따라 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-544">Binding is provided by convention.</span></span> <span data-ttu-id="7ee2f-545">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-545">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="7ee2f-546">**메모리 내 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="7ee2f-546">**In-memory array processing**</span></span>

<span data-ttu-id="7ee2f-547">다음 표에 표시된 구성 키 및 값을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-547">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="7ee2f-548">키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-548">Key</span></span>             | <span data-ttu-id="7ee2f-549">값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-549">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="7ee2f-550">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-550">array:entries:0</span></span> | <span data-ttu-id="7ee2f-551">value0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-551">value0</span></span> |
| <span data-ttu-id="7ee2f-552">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-552">array:entries:1</span></span> | <span data-ttu-id="7ee2f-553">value1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-553">value1</span></span> |
| <span data-ttu-id="7ee2f-554">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-554">array:entries:2</span></span> | <span data-ttu-id="7ee2f-555">value2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-555">value2</span></span> |
| <span data-ttu-id="7ee2f-556">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="7ee2f-556">array:entries:4</span></span> | <span data-ttu-id="7ee2f-557">value4</span><span class="sxs-lookup"><span data-stu-id="7ee2f-557">value4</span></span> |
| <span data-ttu-id="7ee2f-558">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="7ee2f-558">array:entries:5</span></span> | <span data-ttu-id="7ee2f-559">value5</span><span class="sxs-lookup"><span data-stu-id="7ee2f-559">value5</span></span> |

<span data-ttu-id="7ee2f-560">메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-560">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

<span data-ttu-id="7ee2f-561">배열은 인덱스 &num;3에 대한 값을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-561">The array skips a value for index &num;3.</span></span> <span data-ttu-id="7ee2f-562">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-562">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="7ee2f-563">샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-563">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="7ee2f-564">구성 데이터는 개체에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-564">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="7ee2f-565">`GetSection`은 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 패키지에 포함되며, 해당 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-565">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7ee2f-566">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문도 사용할 수 있으며, 이 경우 코드가 더 간결해집니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-566">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="7ee2f-567">바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-567">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="7ee2f-568">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="7ee2f-568">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="7ee2f-569">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-569">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="7ee2f-570">0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-570">0</span></span>                            | <span data-ttu-id="7ee2f-571">value0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-571">value0</span></span>                       |
| <span data-ttu-id="7ee2f-572">1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-572">1</span></span>                            | <span data-ttu-id="7ee2f-573">value1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-573">value1</span></span>                       |
| <span data-ttu-id="7ee2f-574">2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-574">2</span></span>                            | <span data-ttu-id="7ee2f-575">value2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-575">value2</span></span>                       |
| <span data-ttu-id="7ee2f-576">3</span><span class="sxs-lookup"><span data-stu-id="7ee2f-576">3</span></span>                            | <span data-ttu-id="7ee2f-577">value4</span><span class="sxs-lookup"><span data-stu-id="7ee2f-577">value4</span></span>                       |
| <span data-ttu-id="7ee2f-578">4</span><span class="sxs-lookup"><span data-stu-id="7ee2f-578">4</span></span>                            | <span data-ttu-id="7ee2f-579">value5</span><span class="sxs-lookup"><span data-stu-id="7ee2f-579">value5</span></span>                       |

<span data-ttu-id="7ee2f-580">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-580">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="7ee2f-581">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-581">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="7ee2f-582">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-582">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="7ee2f-583">인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-583">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="7ee2f-584">샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-584">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="7ee2f-585">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-585">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="7ee2f-586"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>의 경우</span><span class="sxs-lookup"><span data-stu-id="7ee2f-586">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="7ee2f-587">표에 표시된 키-값 쌍이 구성으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-587">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="7ee2f-588">키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-588">Key</span></span>             | <span data-ttu-id="7ee2f-589">값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-589">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="7ee2f-590">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="7ee2f-590">array:entries:3</span></span> | <span data-ttu-id="7ee2f-591">value3</span><span class="sxs-lookup"><span data-stu-id="7ee2f-591">value3</span></span> |

<span data-ttu-id="7ee2f-592">JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-592">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="7ee2f-593">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="7ee2f-593">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="7ee2f-594">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-594">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="7ee2f-595">0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-595">0</span></span>                            | <span data-ttu-id="7ee2f-596">value0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-596">value0</span></span>                       |
| <span data-ttu-id="7ee2f-597">1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-597">1</span></span>                            | <span data-ttu-id="7ee2f-598">value1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-598">value1</span></span>                       |
| <span data-ttu-id="7ee2f-599">2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-599">2</span></span>                            | <span data-ttu-id="7ee2f-600">value2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-600">value2</span></span>                       |
| <span data-ttu-id="7ee2f-601">3</span><span class="sxs-lookup"><span data-stu-id="7ee2f-601">3</span></span>                            | <span data-ttu-id="7ee2f-602">value3</span><span class="sxs-lookup"><span data-stu-id="7ee2f-602">value3</span></span>                       |
| <span data-ttu-id="7ee2f-603">4</span><span class="sxs-lookup"><span data-stu-id="7ee2f-603">4</span></span>                            | <span data-ttu-id="7ee2f-604">value4</span><span class="sxs-lookup"><span data-stu-id="7ee2f-604">value4</span></span>                       |
| <span data-ttu-id="7ee2f-605">5</span><span class="sxs-lookup"><span data-stu-id="7ee2f-605">5</span></span>                            | <span data-ttu-id="7ee2f-606">value5</span><span class="sxs-lookup"><span data-stu-id="7ee2f-606">value5</span></span>                       |

<span data-ttu-id="7ee2f-607">**JSON 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="7ee2f-607">**JSON array processing**</span></span>

<span data-ttu-id="7ee2f-608">JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-608">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="7ee2f-609">다음 구성 파일에서 `subsection`은 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-609">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="7ee2f-610">JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-610">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="7ee2f-611">키</span><span class="sxs-lookup"><span data-stu-id="7ee2f-611">Key</span></span>                     | <span data-ttu-id="7ee2f-612">값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-612">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="7ee2f-613">json_array:key</span><span class="sxs-lookup"><span data-stu-id="7ee2f-613">json_array:key</span></span>          | <span data-ttu-id="7ee2f-614">valueA</span><span class="sxs-lookup"><span data-stu-id="7ee2f-614">valueA</span></span> |
| <span data-ttu-id="7ee2f-615">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-615">json_array:subsection:0</span></span> | <span data-ttu-id="7ee2f-616">valueB</span><span class="sxs-lookup"><span data-stu-id="7ee2f-616">valueB</span></span> |
| <span data-ttu-id="7ee2f-617">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-617">json_array:subsection:1</span></span> | <span data-ttu-id="7ee2f-618">valueC</span><span class="sxs-lookup"><span data-stu-id="7ee2f-618">valueC</span></span> |
| <span data-ttu-id="7ee2f-619">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-619">json_array:subsection:2</span></span> | <span data-ttu-id="7ee2f-620">valueD</span><span class="sxs-lookup"><span data-stu-id="7ee2f-620">valueD</span></span> |

<span data-ttu-id="7ee2f-621">샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-621">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="7ee2f-622">바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-622">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="7ee2f-623">하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-623">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="7ee2f-624">`JsonArrayExample.Subsection` 인덱스</span><span class="sxs-lookup"><span data-stu-id="7ee2f-624">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="7ee2f-625">`JsonArrayExample.Subsection` 값</span><span class="sxs-lookup"><span data-stu-id="7ee2f-625">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="7ee2f-626">0</span><span class="sxs-lookup"><span data-stu-id="7ee2f-626">0</span></span>                                   | <span data-ttu-id="7ee2f-627">valueB</span><span class="sxs-lookup"><span data-stu-id="7ee2f-627">valueB</span></span>                              |
| <span data-ttu-id="7ee2f-628">1</span><span class="sxs-lookup"><span data-stu-id="7ee2f-628">1</span></span>                                   | <span data-ttu-id="7ee2f-629">valueC</span><span class="sxs-lookup"><span data-stu-id="7ee2f-629">valueC</span></span>                              |
| <span data-ttu-id="7ee2f-630">2</span><span class="sxs-lookup"><span data-stu-id="7ee2f-630">2</span></span>                                   | <span data-ttu-id="7ee2f-631">valueD</span><span class="sxs-lookup"><span data-stu-id="7ee2f-631">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="7ee2f-632">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="7ee2f-632">Custom configuration provider</span></span>

<span data-ttu-id="7ee2f-633">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-633">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="7ee2f-634">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-634">The provider has the following characteristics:</span></span>

* <span data-ttu-id="7ee2f-635">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-635">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="7ee2f-636">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-636">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="7ee2f-637">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-637">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="7ee2f-638">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-638">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="7ee2f-639">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-639">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="7ee2f-640">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-640">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="7ee2f-641">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-641">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="7ee2f-642">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-642">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="7ee2f-643">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-643">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="7ee2f-644"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-644">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="7ee2f-645">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-645">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="7ee2f-646"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-646">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="7ee2f-647">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-647">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="7ee2f-648">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-648">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="7ee2f-649">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-649">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="7ee2f-650">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-650">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="7ee2f-651">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-651">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="7ee2f-652">시작하는 동안 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="7ee2f-652">Access configuration during startup</span></span>

<span data-ttu-id="7ee2f-653">`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-653">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7ee2f-654">`Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-654">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="7ee2f-655">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-655">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="7ee2f-656">Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="7ee2f-656">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="7ee2f-657">Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-657">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="7ee2f-658">Razor 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-658">In a Razor Pages page:</span></span>

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

<span data-ttu-id="7ee2f-659">MVC 뷰에서:</span><span class="sxs-lookup"><span data-stu-id="7ee2f-659">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="7ee2f-660">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="7ee2f-660">Add configuration from an external assembly</span></span>

<span data-ttu-id="7ee2f-661"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>구현에서는 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-661">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="7ee2f-662">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7ee2f-662">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ee2f-663">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7ee2f-663">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* <span data-ttu-id="7ee2f-664">[Deep Dive into Microsoft Configuration](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)(Microsoft 구성 자세히 알아보기)</span><span class="sxs-lookup"><span data-stu-id="7ee2f-664">[Deep Dive into Microsoft Configuration](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)</span></span>
