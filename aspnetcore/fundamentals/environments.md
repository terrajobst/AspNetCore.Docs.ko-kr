---
title: ASP.NET Core에서 여러 환경 사용
author: rick-anderson
description: ASP.NET Core 앱의 여러 환경에서 앱 동작을 제어하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: 30e2771c0a24fcbf6490d08c7028566314b6c011
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358723"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="070dd-103">ASP.NET Core에서 여러 환경 사용</span><span class="sxs-lookup"><span data-stu-id="070dd-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="070dd-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="070dd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="070dd-105">ASP.NET Core는 환경 변수를 사용하여 런타임 환경에 따라 앱 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="070dd-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="070dd-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="070dd-107">환경</span><span class="sxs-lookup"><span data-stu-id="070dd-107">Environments</span></span>

<span data-ttu-id="070dd-108">ASP.NET Core는 앱 시작 시 환경 변수 `ASPNETCORE_ENVIRONMENT`를 읽고 [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName)에 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="070dd-109">`ASPNETCORE_ENVIRONMENT`를 임의의 값으로 설정할 수 있지만 세 개의 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="070dd-110"><xref:Microsoft.Extensions.Hosting.Environments.Production>(기본값)</span><span class="sxs-lookup"><span data-stu-id="070dd-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="070dd-111">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="070dd-111">The preceding code:</span></span>

* <span data-ttu-id="070dd-112">`ASPNETCORE_ENVIRONMENT`이 `Development`로 설정된 경우 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="070dd-113">`ASPNETCORE_ENVIRONMENT`의 값이 다음 중 하나로 설정된 경우 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="070dd-114">[Environment 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)는 `IHostingEnvironment.EnvironmentName`의 값을 사용하여 요소에 태그를 포함하거나 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="070dd-115">Windows 및 macOS에서 환경 변수 및 값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="070dd-116">Linux 환경 변수 및 값은 기본적으로 **대/소문자를 구분**합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="070dd-117">개발</span><span class="sxs-lookup"><span data-stu-id="070dd-117">Development</span></span>

<span data-ttu-id="070dd-118">개발 환경은 프로덕션에서 노출해서는 안 되는 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="070dd-119">예를 들어 ASP.NET Core 템플릿은 개발 환경에서 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="070dd-120">로컬 컴퓨터 개발을 위한 환경은 프로젝트의 *Properties\launchSettings.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="070dd-121">*launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="070dd-122">다음 JSON은 *launchSettings.json* 파일의 세 가지 프로필을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="070dd-123">*launchSettings.json*의 `applicationUrl` 속성은 서버 URL의 목록을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="070dd-124">목록의 URL 사이에 세미콜론을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-124">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="070dd-125">[dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 앱을 시작하면 `"commandName": "Project"`를 포함한 첫 번째 프로필이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="070dd-126">`commandName`의 값은 시작할 웹 서버를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="070dd-127">`commandName`은 다음 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="070dd-128">`Project`(Kestrel을 시작합니다.)</span><span class="sxs-lookup"><span data-stu-id="070dd-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="070dd-129">[dotnet run](/dotnet/core/tools/dotnet-run)으로 앱을 시작하는 경우:</span><span class="sxs-lookup"><span data-stu-id="070dd-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="070dd-130">가능한 경우 *launchSettings.json*을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="070dd-131">*launchSettings.json*의 `environmentVariables` 설정이 환경 변수를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="070dd-132">호스팅 환경이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="070dd-133">다음 출력은 [dotnet run](/dotnet/core/tools/dotnet-run)으로 시작된 앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="070dd-134">Visual Studio 프로젝트 속성 **Debug** 탭은 *launchSettings.json* 파일을 편집할 수 있는 GUI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

<span data-ttu-id="070dd-136">웹 서버가 다시 시작되기 전에는 프로젝트 프로필의 변경 내용이 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="070dd-137">해당 환경에 대한 변경 내용을 감지하려면 Kestrel을 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="070dd-138">*launchSettings.json*은 암호를 저장하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="070dd-139">[암호 관리자 도구](xref:security/app-secrets)를 사용하여 로컬 개발에 사용되는 암호를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="070dd-140">[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 경우 환경 변수는 *.vscode/launch.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="070dd-141">다음 예제는 환경을 `Development`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-141">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="070dd-142">*Properties/launchSettings.json*과 같은 방식으로 `dotnet run`을 사용하여 앱을 시작할 때 프로젝트의 *.vscode/launch.json* 파일은 읽히지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="070dd-143">*launchSettings.json* 파일이 없는 개발 환경에서 앱을 시작할 때는 `dotnet run` 명령에 대한 환경 변수 또는 명령줄 인수로 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="070dd-144">Production</span><span class="sxs-lookup"><span data-stu-id="070dd-144">Production</span></span>

<span data-ttu-id="070dd-145">프로덕션 환경은 보안, 성능 및 앱 견고성을 최대화하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="070dd-146">개발과 다른 몇 가지 일반적인 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="070dd-147">캐싱.</span><span class="sxs-lookup"><span data-stu-id="070dd-147">Caching.</span></span>
* <span data-ttu-id="070dd-148">클라이언트 쪽 리소스가 번들로 제공되고, 축소되며, 잠재적으로 CDN에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="070dd-149">진단 오류 페이지를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="070dd-150">친숙한 오류 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="070dd-151">프로덕션 로깅 및 모니터링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="070dd-152">예: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="070dd-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="070dd-153">환경 설정</span><span class="sxs-lookup"><span data-stu-id="070dd-153">Set the environment</span></span>

<span data-ttu-id="070dd-154">환경 변수 또는 플랫폼 설정을 사용하여 테스트하기 위해 특정 환경을 설정하면 유용한 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="070dd-155">환경을 설정하지 않으면 대부분의 디버깅 기능을 사용하지 않는 `Production`으로 기본값이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="070dd-156">환경을 설정하는 방법은 운영 체제에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="070dd-157">호스트를 빌드할 때 앱에서 읽은 마지막 환경 설정에 따라 앱의 환경이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="070dd-158">앱을 실행하는 동안에는 앱 환경을 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="070dd-159">환경 변수 또는 플랫폼 설정</span><span class="sxs-lookup"><span data-stu-id="070dd-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="070dd-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="070dd-160">Azure App Service</span></span>

<span data-ttu-id="070dd-161">[Azure App Service](https://azure.microsoft.com/services/app-service/)에서 환경을 설정하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="070dd-162">**App Services** 블레이드에서 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="070dd-163">**설정** 그룹에서 **구성** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="070dd-164">**애플리케이션 설정** 탭에서 **새 애플리케이션 설정**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="070dd-165">**애플리케이션 설정 추가/편집** 창에서 **이름**에 대한 `ASPNETCORE_ENVIRONMENT`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="070dd-166">**값**에는 환경을 입력합니다(예: `Staging`).</span><span class="sxs-lookup"><span data-stu-id="070dd-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="070dd-167">배포 슬롯을 교환할 때 환경 설정을 현재 슬롯으로 유지하려면 **배포 슬롯 설정** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="070dd-168">자세한 내용은 Azure 설명서의 [Azure App Service에서 스테이징 환경 설정](/azure/app-service/web-sites-staged-publishing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="070dd-169">**확인**을 선택하여 **애플리케이션 설정 추가/편집** 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="070dd-170">**구성** 블레이드의 맨 위에 있는 **저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="070dd-171">Azure App Service는 Azure 포털에서 앱 설정(환경 변수)이 추가, 변경 또는 삭제된 후 앱을 자동으로 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="070dd-172">Windows</span><span class="sxs-lookup"><span data-stu-id="070dd-172">Windows</span></span>

<span data-ttu-id="070dd-173">앱이 [dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 시작할 때 현재 세션에 대한 `ASPNETCORE_ENVIRONMENT`를 설정하려면 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="070dd-174">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="070dd-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="070dd-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="070dd-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="070dd-176">이 명령들은 현재 창에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="070dd-177">창이 닫히면 `ASPNETCORE_ENVIRONMENT` 설정이 기본 설정 또는 컴퓨터 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="070dd-178">Windows에서 전역적으로 값을 설정하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="070dd-179">**제어판** > **시스템** > **고급 시스템 설정**을 열고 `ASPNETCORE_ENVIRONMENT` 값을 추가하거나 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![시스템 고급 속성](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 환경 변수](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="070dd-182">관리자 권한의 명령 프롬프트를 열고 `setx` 명령을 사용하거나 관리자 권한의 PowerShell 명령 프롬프트를 열고 `[Environment]::SetEnvironmentVariable`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="070dd-183">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="070dd-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="070dd-184">`/M` 스위치는 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="070dd-185">`/M` 스위치를 사용하지 않으면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="070dd-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="070dd-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="070dd-187">`Machine` 옵션 값은 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="070dd-188">옵션 값이 `User`로 변경되면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="070dd-189">`ASPNETCORE_ENVIRONMENT` 환경 변수를 전역적으로 설정하면 값이 설정된 후 열리는 모든 명령 창에서 `dotnet run`에 대해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="070dd-190">**web.config**</span><span class="sxs-lookup"><span data-stu-id="070dd-190">**web.config**</span></span>

<span data-ttu-id="070dd-191">*web.config*를 사용하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>의 *환경 변수 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="070dd-192">**프로젝트 파일 또는 게시 프로필**</span><span class="sxs-lookup"><span data-stu-id="070dd-192">**Project file or publish profile**</span></span>

<span data-ttu-id="070dd-193">**Windows IIS 배포의 경우:** `<EnvironmentName>` 속성을 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="070dd-194">이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="070dd-195">**IIS 응용 프로그램 풀 별**</span><span class="sxs-lookup"><span data-stu-id="070dd-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="070dd-196">격리된 응용 프로그램 풀에서 실행되는(IIS 10.0 이상에서 지원됨) 앱에 대한 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면, [환경 변수 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="070dd-197">`ASPNETCORE_ENVIRONMENT` 환경 변수를 앱 풀에 대해 설정하면 해당 값이 시스템 수준의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="070dd-198">IIS에서 앱을 호스팅하고 `ASPNETCORE_ENVIRONMENT` 환경 변수를 추가 또는 변경하는 경우 다음 방법 중 하나를 사용하여 앱에서 새 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="070dd-199">명령 프롬프트에서 `net stop was /y` 다음에 `net start w3svc`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="070dd-200">서버를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="070dd-201">macOS</span><span class="sxs-lookup"><span data-stu-id="070dd-201">macOS</span></span>

<span data-ttu-id="070dd-202">macOS에 대한 현재 환경 설정은 앱을 실행할 때 인라인으로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="070dd-203">또는 앱을 실행하기 전에 `export`를 사용하여 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="070dd-204">컴퓨터 수준 환경 변수는 *.bashrc* 또는 *.bash_profile* 파일에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="070dd-205">임의의 텍스트 편집기를 사용하여 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-205">Edit the file using any text editor.</span></span> <span data-ttu-id="070dd-206">다음 명령문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="070dd-207">Linux</span><span class="sxs-lookup"><span data-stu-id="070dd-207">Linux</span></span>

<span data-ttu-id="070dd-208">Linux 배포판의 경우 세션 기반 변수 설정을 위해 명령 프롬프트에서 `export` 명령을 사용하거나 컴퓨터 수준 환경 설정을 위해 *bash_profile* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="070dd-209">코드에서 환경 설정</span><span class="sxs-lookup"><span data-stu-id="070dd-209">Set the environment in code</span></span>

<span data-ttu-id="070dd-210">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="070dd-211"><xref:fundamentals/host/generic-host#environmentname>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="070dd-212">환경별 구성</span><span class="sxs-lookup"><span data-stu-id="070dd-212">Configuration by environment</span></span>

<span data-ttu-id="070dd-213">환경별 구성을 로드하려면 다음을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="070dd-214">*appsettings* 파일(*appsettings.{Environment}.json*).</span><span class="sxs-lookup"><span data-stu-id="070dd-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="070dd-215"><xref:fundamentals/configuration/index#json-configuration-provider>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="070dd-216">환경 변수(앱이 호스팅되는 각 시스템에서 설정).</span><span class="sxs-lookup"><span data-stu-id="070dd-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="070dd-217"><xref:fundamentals/host/generic-host#environmentname> 및 <xref:security/app-secrets#environment-variables>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="070dd-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="070dd-218">비밀 관리자(개발 환경에만 해당).</span><span class="sxs-lookup"><span data-stu-id="070dd-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="070dd-219"><xref:security/app-secrets>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="070dd-220">환경에 따른 시작 클래스 및 메서드</span><span class="sxs-lookup"><span data-stu-id="070dd-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="070dd-221">IWebHostEnvironment를 Startup.Configure에 삽입</span><span class="sxs-lookup"><span data-stu-id="070dd-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="070dd-222">`Startup.Configure`에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="070dd-223">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 `Startup.Configure`만 조정해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="070dd-224">IWebHostEnvironment를 Startup 클래스에 삽입</span><span class="sxs-lookup"><span data-stu-id="070dd-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="070dd-225">`Startup` 생성자에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="070dd-226">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 `Startup`을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="070dd-227">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="070dd-227">In the following example:</span></span>

* <span data-ttu-id="070dd-228">환경은 `_env` 필드에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="070dd-229">`_env`는 `ConfigureServices` 및 `Configure`에서 사용되어 앱의 환경에 따라 시작 구성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```
### <a name="startup-class-conventions"></a><span data-ttu-id="070dd-230">시작 클래스 규칙</span><span class="sxs-lookup"><span data-stu-id="070dd-230">Startup class conventions</span></span>

<span data-ttu-id="070dd-231">ASP.NET Core 앱이 시작되면 [Startup 클래스](xref:fundamentals/startup)가 앱을 부트스트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="070dd-232">앱은 다양한 환경에 대한 별도의 `Startup` 클래스를 정의할 수 있습니다(예: `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="070dd-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="070dd-233">런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="070dd-234">이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="070dd-235">일치하는 `Startup{EnvironmentName}` 클래스를 찾을 수 없으면 `Startup` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="070dd-236">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="070dd-237">환경 기반 `Startup` 클래스를 구현하려면 사용 중인 각 환경에 대한 `Startup{EnvironmentName}` 클래스와 폴백 `Startup` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="070dd-238">어셈블리 이름을 허용하는 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="070dd-239">시작 메서드 규칙</span><span class="sxs-lookup"><span data-stu-id="070dd-239">Startup method conventions</span></span>

<span data-ttu-id="070dd-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)는 `Configure<EnvironmentName>` 및 `Configure<EnvironmentName>Services` 양식의 환경 특정 버전을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="070dd-241">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="070dd-242">추가 자료</span><span class="sxs-lookup"><span data-stu-id="070dd-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="070dd-243">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="070dd-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="070dd-244">ASP.NET Core는 환경 변수를 사용하여 런타임 환경에 따라 앱 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="070dd-245">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="070dd-245">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="070dd-246">환경</span><span class="sxs-lookup"><span data-stu-id="070dd-246">Environments</span></span>

<span data-ttu-id="070dd-247">ASP.NET Core는 앱 시작 시 환경 변수 `ASPNETCORE_ENVIRONMENT`를 읽고 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName)에 그 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="070dd-248">`ASPNETCORE_ENVIRONMENT`를 임의의 값으로 설정할 수 있지만 세 개의 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="070dd-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(기본값)</span><span class="sxs-lookup"><span data-stu-id="070dd-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="070dd-250">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="070dd-250">The preceding code:</span></span>

* <span data-ttu-id="070dd-251">`ASPNETCORE_ENVIRONMENT`이 `Development`로 설정된 경우 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="070dd-252">`ASPNETCORE_ENVIRONMENT`의 값이 다음 중 하나로 설정된 경우 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="070dd-253">[Environment 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)는 `IHostingEnvironment.EnvironmentName`의 값을 사용하여 요소에 태그를 포함하거나 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="070dd-254">Windows 및 macOS에서 환경 변수 및 값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="070dd-255">Linux 환경 변수 및 값은 기본적으로 **대/소문자를 구분**합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="070dd-256">개발</span><span class="sxs-lookup"><span data-stu-id="070dd-256">Development</span></span>

<span data-ttu-id="070dd-257">개발 환경은 프로덕션에서 노출해서는 안 되는 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="070dd-258">예를 들어 ASP.NET Core 템플릿은 개발 환경에서 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="070dd-259">로컬 컴퓨터 개발을 위한 환경은 프로젝트의 *Properties\launchSettings.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="070dd-260">*launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="070dd-261">다음 JSON은 *launchSettings.json* 파일의 세 가지 프로필을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="070dd-262">*launchSettings.json*의 `applicationUrl` 속성은 서버 URL의 목록을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="070dd-263">목록의 URL 사이에 세미콜론을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-263">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="070dd-264">[dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 앱을 시작하면 `"commandName": "Project"`를 포함한 첫 번째 프로필이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="070dd-265">`commandName`의 값은 시작할 웹 서버를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="070dd-266">`commandName`은 다음 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="070dd-267">`Project`(Kestrel을 시작합니다.)</span><span class="sxs-lookup"><span data-stu-id="070dd-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="070dd-268">[dotnet run](/dotnet/core/tools/dotnet-run)으로 앱을 시작하는 경우:</span><span class="sxs-lookup"><span data-stu-id="070dd-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="070dd-269">가능한 경우 *launchSettings.json*을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="070dd-270">*launchSettings.json*의 `environmentVariables` 설정이 환경 변수를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="070dd-271">호스팅 환경이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="070dd-272">다음 출력은 [dotnet run](/dotnet/core/tools/dotnet-run)으로 시작된 앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="070dd-273">Visual Studio 프로젝트 속성 **Debug** 탭은 *launchSettings.json* 파일을 편집할 수 있는 GUI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

<span data-ttu-id="070dd-275">웹 서버가 다시 시작되기 전에는 프로젝트 프로필의 변경 내용이 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="070dd-276">해당 환경에 대한 변경 내용을 감지하려면 Kestrel을 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="070dd-277">*launchSettings.json*은 암호를 저장하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="070dd-278">[암호 관리자 도구](xref:security/app-secrets)를 사용하여 로컬 개발에 사용되는 암호를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="070dd-279">[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 경우 환경 변수는 *.vscode/launch.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="070dd-280">다음 예제는 환경을 `Development`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-280">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="070dd-281">*Properties/launchSettings.json*과 같은 방식으로 `dotnet run`을 사용하여 앱을 시작할 때 프로젝트의 *.vscode/launch.json* 파일은 읽히지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="070dd-282">*launchSettings.json* 파일이 없는 개발 환경에서 앱을 시작할 때는 `dotnet run` 명령에 대한 환경 변수 또는 명령줄 인수로 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="070dd-283">Production</span><span class="sxs-lookup"><span data-stu-id="070dd-283">Production</span></span>

<span data-ttu-id="070dd-284">프로덕션 환경은 보안, 성능 및 앱 견고성을 최대화하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="070dd-285">개발과 다른 몇 가지 일반적인 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="070dd-286">캐싱.</span><span class="sxs-lookup"><span data-stu-id="070dd-286">Caching.</span></span>
* <span data-ttu-id="070dd-287">클라이언트 쪽 리소스가 번들로 제공되고, 축소되며, 잠재적으로 CDN에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="070dd-288">진단 오류 페이지를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="070dd-289">친숙한 오류 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="070dd-290">프로덕션 로깅 및 모니터링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="070dd-291">예: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="070dd-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="070dd-292">환경 설정</span><span class="sxs-lookup"><span data-stu-id="070dd-292">Set the environment</span></span>

<span data-ttu-id="070dd-293">환경 변수 또는 플랫폼 설정을 사용하여 테스트하기 위해 특정 환경을 설정하면 유용한 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="070dd-294">환경을 설정하지 않으면 대부분의 디버깅 기능을 사용하지 않는 `Production`으로 기본값이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="070dd-295">환경을 설정하는 방법은 운영 체제에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="070dd-296">호스트를 빌드할 때 앱에서 읽은 마지막 환경 설정에 따라 앱의 환경이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="070dd-297">앱을 실행하는 동안에는 앱 환경을 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="070dd-298">환경 변수 또는 플랫폼 설정</span><span class="sxs-lookup"><span data-stu-id="070dd-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="070dd-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="070dd-299">Azure App Service</span></span>

<span data-ttu-id="070dd-300">[Azure App Service](https://azure.microsoft.com/services/app-service/)에서 환경을 설정하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="070dd-301">**App Services** 블레이드에서 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="070dd-302">**설정** 그룹에서 **구성** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="070dd-303">**애플리케이션 설정** 탭에서 **새 애플리케이션 설정**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="070dd-304">**애플리케이션 설정 추가/편집** 창에서 **이름**에 대한 `ASPNETCORE_ENVIRONMENT`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="070dd-305">**값**에는 환경을 입력합니다(예: `Staging`).</span><span class="sxs-lookup"><span data-stu-id="070dd-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="070dd-306">배포 슬롯을 교환할 때 환경 설정을 현재 슬롯으로 유지하려면 **배포 슬롯 설정** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="070dd-307">자세한 내용은 Azure 설명서의 [Azure App Service에서 스테이징 환경 설정](/azure/app-service/web-sites-staged-publishing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="070dd-308">**확인**을 선택하여 **애플리케이션 설정 추가/편집** 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="070dd-309">**구성** 블레이드의 맨 위에 있는 **저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="070dd-310">Azure App Service는 Azure 포털에서 앱 설정(환경 변수)이 추가, 변경 또는 삭제된 후 앱을 자동으로 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="070dd-311">Windows</span><span class="sxs-lookup"><span data-stu-id="070dd-311">Windows</span></span>

<span data-ttu-id="070dd-312">앱이 [dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 시작할 때 현재 세션에 대한 `ASPNETCORE_ENVIRONMENT`를 설정하려면 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="070dd-313">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="070dd-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="070dd-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="070dd-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="070dd-315">이 명령들은 현재 창에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="070dd-316">창이 닫히면 `ASPNETCORE_ENVIRONMENT` 설정이 기본 설정 또는 컴퓨터 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="070dd-317">Windows에서 전역적으로 값을 설정하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="070dd-318">**제어판** > **시스템** > **고급 시스템 설정**을 열고 `ASPNETCORE_ENVIRONMENT` 값을 추가하거나 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![시스템 고급 속성](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 환경 변수](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="070dd-321">관리자 권한의 명령 프롬프트를 열고 `setx` 명령을 사용하거나 관리자 권한의 PowerShell 명령 프롬프트를 열고 `[Environment]::SetEnvironmentVariable`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="070dd-322">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="070dd-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="070dd-323">`/M` 스위치는 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="070dd-324">`/M` 스위치를 사용하지 않으면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="070dd-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="070dd-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="070dd-326">`Machine` 옵션 값은 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="070dd-327">옵션 값이 `User`로 변경되면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="070dd-328">`ASPNETCORE_ENVIRONMENT` 환경 변수를 전역적으로 설정하면 값이 설정된 후 열리는 모든 명령 창에서 `dotnet run`에 대해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="070dd-329">**web.config**</span><span class="sxs-lookup"><span data-stu-id="070dd-329">**web.config**</span></span>

<span data-ttu-id="070dd-330">*web.config*를 사용하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>의 *환경 변수 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="070dd-331">**프로젝트 파일 또는 게시 프로필**</span><span class="sxs-lookup"><span data-stu-id="070dd-331">**Project file or publish profile**</span></span>

<span data-ttu-id="070dd-332">**Windows IIS 배포의 경우:** `<EnvironmentName>` 속성을 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="070dd-333">이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="070dd-334">**IIS 응용 프로그램 풀 별**</span><span class="sxs-lookup"><span data-stu-id="070dd-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="070dd-335">격리된 응용 프로그램 풀에서 실행되는(IIS 10.0 이상에서 지원됨) 앱에 대한 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면, [환경 변수 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="070dd-336">`ASPNETCORE_ENVIRONMENT` 환경 변수를 앱 풀에 대해 설정하면 해당 값이 시스템 수준의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="070dd-337">IIS에서 앱을 호스팅하고 `ASPNETCORE_ENVIRONMENT` 환경 변수를 추가 또는 변경하는 경우 다음 방법 중 하나를 사용하여 앱에서 새 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="070dd-338">명령 프롬프트에서 `net stop was /y` 다음에 `net start w3svc`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="070dd-339">서버를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="070dd-340">macOS</span><span class="sxs-lookup"><span data-stu-id="070dd-340">macOS</span></span>

<span data-ttu-id="070dd-341">macOS에 대한 현재 환경 설정은 앱을 실행할 때 인라인으로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="070dd-342">또는 앱을 실행하기 전에 `export`를 사용하여 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="070dd-343">컴퓨터 수준 환경 변수는 *.bashrc* 또는 *.bash_profile* 파일에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="070dd-344">임의의 텍스트 편집기를 사용하여 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-344">Edit the file using any text editor.</span></span> <span data-ttu-id="070dd-345">다음 명령문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="070dd-346">Linux</span><span class="sxs-lookup"><span data-stu-id="070dd-346">Linux</span></span>

<span data-ttu-id="070dd-347">Linux 배포판의 경우 세션 기반 변수 설정을 위해 명령 프롬프트에서 `export` 명령을 사용하거나 컴퓨터 수준 환경 설정을 위해 *bash_profile* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="070dd-348">코드에서 환경 설정</span><span class="sxs-lookup"><span data-stu-id="070dd-348">Set the environment in code</span></span>

<span data-ttu-id="070dd-349">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="070dd-350"><xref:fundamentals/host/web-host#environment>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="070dd-351">환경별 구성</span><span class="sxs-lookup"><span data-stu-id="070dd-351">Configuration by environment</span></span>

<span data-ttu-id="070dd-352">환경별 구성을 로드하려면 다음을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="070dd-353">*appsettings* 파일(*appsettings.{Environment}.json*).</span><span class="sxs-lookup"><span data-stu-id="070dd-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="070dd-354"><xref:fundamentals/configuration/index#json-configuration-provider>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="070dd-355">환경 변수(앱이 호스팅되는 각 시스템에서 설정).</span><span class="sxs-lookup"><span data-stu-id="070dd-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="070dd-356"><xref:fundamentals/host/web-host#environment> 및 <xref:security/app-secrets#environment-variables>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="070dd-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="070dd-357">비밀 관리자(개발 환경에만 해당).</span><span class="sxs-lookup"><span data-stu-id="070dd-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="070dd-358"><xref:security/app-secrets>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="070dd-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="070dd-359">환경에 따른 시작 클래스 및 메서드</span><span class="sxs-lookup"><span data-stu-id="070dd-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="070dd-360">IHostingEnvironment를 Startup.Configure에 삽입</span><span class="sxs-lookup"><span data-stu-id="070dd-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="070dd-361">`Startup.Configure`에 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="070dd-362">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 `Startup.Configure`를 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="070dd-363">IHostingEnvironment를 Startup 클래스에 삽입</span><span class="sxs-lookup"><span data-stu-id="070dd-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="070dd-364">`Startup` 생성자에 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>를 삽입하고 `Startup` 클래스 전체에서 하기 위해 서비스를 필드에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="070dd-365">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="070dd-366">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="070dd-366">In the following example:</span></span>

* <span data-ttu-id="070dd-367">환경은 `_env` 필드에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="070dd-368">`_env`는 `ConfigureServices` 및 `Configure`에서 사용되어 앱의 환경에 따라 시작 구성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="070dd-369">시작 클래스 규칙</span><span class="sxs-lookup"><span data-stu-id="070dd-369">Startup class conventions</span></span>

<span data-ttu-id="070dd-370">ASP.NET Core 앱이 시작되면 [Startup 클래스](xref:fundamentals/startup)가 앱을 부트스트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="070dd-371">앱은 다양한 환경에 대한 별도의 `Startup` 클래스를 정의할 수 있습니다(예: `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="070dd-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="070dd-372">런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="070dd-373">이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="070dd-374">일치하는 `Startup{EnvironmentName}` 클래스를 찾을 수 없으면 `Startup` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="070dd-375">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="070dd-376">환경 기반 `Startup` 클래스를 구현하려면 사용 중인 각 환경에 대한 `Startup{EnvironmentName}` 클래스와 폴백 `Startup` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="070dd-377">어셈블리 이름을 허용하는 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="070dd-378">시작 메서드 규칙</span><span class="sxs-lookup"><span data-stu-id="070dd-378">Startup method conventions</span></span>

<span data-ttu-id="070dd-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)는 `Configure<EnvironmentName>` 및 `Configure<EnvironmentName>Services` 양식의 환경 특정 버전을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="070dd-380">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="070dd-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="070dd-381">추가 자료</span><span class="sxs-lookup"><span data-stu-id="070dd-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
