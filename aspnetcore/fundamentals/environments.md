---
title: ASP.NET Core에서 여러 환경 사용
author: rick-anderson
description: ASP.NET Core 앱의 여러 환경에서 앱 동작을 제어하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2019
uid: fundamentals/environments
ms.openlocfilehash: a0e6d62f352a886a9bc051813a21d94c1605a1ce
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087038"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="e6cf1-103">ASP.NET Core에서 여러 환경 사용</span><span class="sxs-lookup"><span data-stu-id="e6cf1-103">Use multiple environments in ASP.NET Core</span></span>

<span data-ttu-id="e6cf1-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6cf1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e6cf1-105">ASP.NET Core는 환경 변수를 사용하여 런타임 환경에 따라 앱 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="e6cf1-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6cf1-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="e6cf1-107">환경</span><span class="sxs-lookup"><span data-stu-id="e6cf1-107">Environments</span></span>

<span data-ttu-id="e6cf1-108">ASP.NET Core는 앱 시작 시 환경 변수 `ASPNETCORE_ENVIRONMENT`를 읽고 [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)에 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span></span> <span data-ttu-id="e6cf1-109">`ASPNETCORE_ENVIRONMENT`를 임의의 값으로 설정할 수 있지만 [세 개의 값](/dotnet/api/microsoft.aspnetcore.hosting.environmentname)은 프레임워크에서 지원됩니다. [개발](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [스테이징](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) 및 [프로덕션](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span><span class="sxs-lookup"><span data-stu-id="e6cf1-109">You can set `ASPNETCORE_ENVIRONMENT` to any value, but [three values](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) are supported by the framework: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), and [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span></span> <span data-ttu-id="e6cf1-110">`ASPNETCORE_ENVIRONMENT`가 설정되지 않은 경우 기본값은 `Production`입니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-110">If `ASPNETCORE_ENVIRONMENT` isn't set, it defaults to `Production`.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="e6cf1-111">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="e6cf1-111">The preceding code:</span></span>

* <span data-ttu-id="e6cf1-112">`ASPNETCORE_ENVIRONMENT`이 `Development`로 설정된 경우 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="e6cf1-113">`ASPNETCORE_ENVIRONMENT`의 값이 다음 중 하나로 설정된 경우 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="e6cf1-114">[환경 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)는 `IHostingEnvironment.EnvironmentName`의 값을 사용하여 요소에 표시를 포함하거나 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="e6cf1-115">Windows와 macOS에서 환경 변수 및 값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="e6cf1-116">Linux 환경 변수 및 값은 기본적으로 **대/소문자를 구분**합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="e6cf1-117">개발</span><span class="sxs-lookup"><span data-stu-id="e6cf1-117">Development</span></span>

<span data-ttu-id="e6cf1-118">개발 환경은 프로덕션에서 노출해서는 안 되는 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="e6cf1-119">예를 들어 ASP.NET Core 템플릿은 개발 환경에서 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="e6cf1-120">로컬 컴퓨터 개발을 위한 환경은 프로젝트의 *Properties\launchSettings.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="e6cf1-121">*launchSettings.json*의 환경 값은 시스템 환경에서 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="e6cf1-122">다음 JSON에는 *launchSettings.json* 파일의 세 가지 프로필이 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="e6cf1-123">*launchSettings.json*의 `applicationUrl` 속성은 서버 URL의 목록을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="e6cf1-124">목록의 URL 사이에 세미콜론을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="e6cf1-125">[dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 앱을 시작하면 `"commandName": "Project"`를 포함한 첫 번째 프로필이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="e6cf1-126">`commandName`의 값은 시작할 웹 서버를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="e6cf1-127">`commandName`은 다음 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="e6cf1-128">`Project`(Kestrel 시작)</span><span class="sxs-lookup"><span data-stu-id="e6cf1-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="e6cf1-129">앱이 [dotnet run](/dotnet/core/tools/dotnet-run)으로 시작하는 경우:</span><span class="sxs-lookup"><span data-stu-id="e6cf1-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="e6cf1-130">가능한 경우 *launchSettings.json*을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="e6cf1-131">*launchSettings.json*의 `environmentVariables` 설정은 환경 변수를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="e6cf1-132">호스팅 환경이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="e6cf1-133">다음 출력은 [dotnet run](/dotnet/core/tools/dotnet-run)으로 시작되는 앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="e6cf1-134">Visual Studio 프로젝트 속성 **Debug** 탭은 *launchSettings.json* 파일을 편집할 수 있는 GUI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

<span data-ttu-id="e6cf1-136">웹 서버가 다시 시작되기 전에는 프로젝트 프로필의 변경 내용이 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="e6cf1-137">해당 환경에 대한 변경 내용을 감지하려면 Kestrel을 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="e6cf1-138">*launchSettings.json*은 암호를 저장하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="e6cf1-139">[암호 관리자 도구](xref:security/app-secrets)를 사용하여 로컬 개발에 대한 암호를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="e6cf1-140">[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 경우 환경 변수는 *.vscode/launch.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="e6cf1-141">다음 예제에서는 환경을 `Development`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="e6cf1-142">*Properties/launchSettings.json*과 같은 방식으로 `dotnet run`을 사용하여 앱을 시작할 때 프로젝트의 *.vscode/launch.json* 파일은 읽히지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="e6cf1-143">*launchSettings.json* 파일이 없는 개발 환경에서 앱을 시작할 때는 `dotnet run` 명령에 대한 환경 변수 또는 명령줄 인수로 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="e6cf1-144">프로덕션</span><span class="sxs-lookup"><span data-stu-id="e6cf1-144">Production</span></span>

<span data-ttu-id="e6cf1-145">프로덕션 환경은 보안, 성능 및 앱 견고성을 최대화하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="e6cf1-146">개발과 다른 몇 가지 일반적인 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="e6cf1-147">캐싱.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-147">Caching.</span></span>
* <span data-ttu-id="e6cf1-148">클라이언트 쪽 리소스가 번들로 제공되고, 축소되며, 잠재적으로 CDN에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="e6cf1-149">진단 오류 페이지를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="e6cf1-150">친숙한 오류 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="e6cf1-151">프로덕션 로깅 및 모니터링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="e6cf1-152">예: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="e6cf1-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="e6cf1-153">환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-153">Set the environment</span></span>

<span data-ttu-id="e6cf1-154">테스트를 위해 특정 환경을 설정하는 것이 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-154">It's often useful to set a specific environment for testing.</span></span> <span data-ttu-id="e6cf1-155">환경을 설정하지 않으면 대부분의 디버깅 기능을 사용하지 않는 `Production`으로 기본값이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="e6cf1-156">환경 설정에 대한 메서드는 운영 체제에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-156">The method for setting the environment depends on the operating system.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="e6cf1-157">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e6cf1-157">Azure App Service</span></span>

<span data-ttu-id="e6cf1-158">[Azure App Service](https://azure.microsoft.com/services/app-service/)에서 환경을 설정하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-158">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="e6cf1-159">**App Services** 블레이드에서 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-159">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="e6cf1-160">**SETTINGS** 그룹에서 **애플리케이션 설정** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-160">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="e6cf1-161">**애플리케이션 설정** 영역에서 **새 설정 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-161">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="e6cf1-162">**이름 입력**의 경우, `ASPNETCORE_ENVIRONMENT`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-162">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="e6cf1-163">**값 입력**의 경우 환경을 제공합니다(예: `Staging`).</span><span class="sxs-lookup"><span data-stu-id="e6cf1-163">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="e6cf1-164">배포 슬롯을 교환할 때 환경 설정을 현재 슬롯으로 유지하려면 **슬롯 설정** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-164">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="e6cf1-165">자세한 내용은 [Azure 설명서: 교환된 설정은? ](/azure/app-service/web-sites-staged-publishing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-165">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="e6cf1-166">블레이드 상단에서 **저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-166">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="e6cf1-167">Azure App Service는 Azure Portal에서 앱 설정(환경 변수)이 추가, 변경 또는 삭제된 후 앱을 자동으로 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-167">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="e6cf1-168">Windows</span><span class="sxs-lookup"><span data-stu-id="e6cf1-168">Windows</span></span>

<span data-ttu-id="e6cf1-169">현재 세션에 `ASPNETCORE_ENVIRONMENT`를 설정하려면 앱이 [dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 시작할 때 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-169">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="e6cf1-170">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="e6cf1-170">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="e6cf1-171">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="e6cf1-171">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="e6cf1-172">이러한 명령은 현재 창에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-172">These commands only take effect for the current window.</span></span> <span data-ttu-id="e6cf1-173">창이 닫히면 `ASPNETCORE_ENVIRONMENT` 설정이 기본 설정 또는 컴퓨터 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-173">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="e6cf1-174">Windows에서 전역적으로 값을 설정하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-174">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="e6cf1-175">**제어판** > **시스템** > **고급 시스템 설정**을 열고 `ASPNETCORE_ENVIRONMENT` 값을 추가하거나 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-175">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![시스템 고급 속성](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 환경 변수](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="e6cf1-178">관리 명령 프롬프트를 열고 `setx` 명령을 사용하거나 관리 PowerShell 명령 프롬프트를 열고 `[Environment]::SetEnvironmentVariable`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-178">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="e6cf1-179">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="e6cf1-179">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="e6cf1-180">`/M` 스위치는 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-180">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="e6cf1-181">`/M` 스위치를 사용하지 않으면 환경 변수가 사용자 계정으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-181">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="e6cf1-182">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="e6cf1-182">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="e6cf1-183">`Machine` 옵션 값은 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-183">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="e6cf1-184">옵션 값이 `User`로 변경되면 환경 변수가 사용자 계정으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-184">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="e6cf1-185">`ASPNETCORE_ENVIRONMENT` 환경 변수를 전역적으로 설정하면 값이 설정된 후 열리는 모든 명령 창에서 `dotnet run`에 대해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-185">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="e6cf1-186">**web.config**</span><span class="sxs-lookup"><span data-stu-id="e6cf1-186">**web.config**</span></span>

<span data-ttu-id="e6cf1-187">*web.config*를 사용하여 `ASPNETCORE_ENVIRONMENT`환경 변수를 설정하려면 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>의 *환경 변수 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-187">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e6cf1-188">**프로젝트 파일 또는 게시 프로필**</span><span class="sxs-lookup"><span data-stu-id="e6cf1-188">**Project file or publish profile**</span></span>

<span data-ttu-id="e6cf1-189">**Windows IIS 배포의 경우:** `<EnvironmentName>` 속성을 게시 프로필(*.pubxml*) 또는 프로젝트 파일에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-189">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="e6cf1-190">이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-190">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

<span data-ttu-id="e6cf1-191">**IIS 애플리케이션 풀마다**</span><span class="sxs-lookup"><span data-stu-id="e6cf1-191">**Per IIS Application Pool**</span></span>

<span data-ttu-id="e6cf1-192">격리된 애플리케이션 풀에서 실행되는(IIS 10.0 이상에서 지원됨) 앱에 대한 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면, [ 환경 변수 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-192">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="e6cf1-193">`ASPNETCORE_ENVIRONMENT` 환경 변수를 앱 풀에 대해 설정하면 해당 값이 시스템 수준의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-193">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e6cf1-194">IIS에서 앱을 호스팅하고 `ASPNETCORE_ENVIRONMENT` 환경 변수를 추가 또는 변경할 때 다음 방법 중 하나를 사용하여 앱에서 선택한 새 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-194">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="e6cf1-195">명령 프롬프트에서 `net stop was /y` 다음에 `net start w3svc`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-195">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="e6cf1-196">서버를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-196">Restart the server.</span></span>

### <a name="macos"></a><span data-ttu-id="e6cf1-197">macOS</span><span class="sxs-lookup"><span data-stu-id="e6cf1-197">macOS</span></span>

<span data-ttu-id="e6cf1-198">macOS에 대한 현재 환경 설정은 앱을 실행할 때 인라인으로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-198">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="e6cf1-199">또는 앱을 실행하기 전에 `export`를 사용하여 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-199">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="e6cf1-200">컴퓨터 수준 환경 변수는 *.bashrc* 또는 *.bash_profile* 파일에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-200">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="e6cf1-201">임의의 텍스트 편집기를 사용하여 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-201">Edit the file using any text editor.</span></span> <span data-ttu-id="e6cf1-202">다음 명령문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-202">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a><span data-ttu-id="e6cf1-203">Linux</span><span class="sxs-lookup"><span data-stu-id="e6cf1-203">Linux</span></span>

<span data-ttu-id="e6cf1-204">Linux 배포의 경우 세션 기반 변수 설정에 대한 명령 프롬프트에서 `export` 명령 또는 컴퓨터 수준 환경 설정에 대한 *bash_profile* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-204">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="e6cf1-205">환경별 구성</span><span class="sxs-lookup"><span data-stu-id="e6cf1-205">Configuration by environment</span></span>

<span data-ttu-id="e6cf1-206">환경별 구성을 로드하려면 다음을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-206">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="e6cf1-207">*appsettings* 파일(*appsettings.\<Environment>.json*)</span><span class="sxs-lookup"><span data-stu-id="e6cf1-207">*appsettings* files (*appsettings.\<Environment>.json*).</span></span> <span data-ttu-id="e6cf1-208">[구성: 파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-208">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider).</span></span>
* <span data-ttu-id="e6cf1-209">환경 변수(앱이 호스팅되는 각 시스템에서 설정)</span><span class="sxs-lookup"><span data-stu-id="e6cf1-209">environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="e6cf1-210">[구성: 파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider) 및 [개발에서 앱 비밀의 안전한 스토리지: 환경 변수](xref:security/app-secrets#environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-210">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider) and [Safe storage of app secrets in development: Environment variables](xref:security/app-secrets#environment-variables).</span></span>
* <span data-ttu-id="e6cf1-211">비밀 관리자(개발 환경에만 해당)</span><span class="sxs-lookup"><span data-stu-id="e6cf1-211">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="e6cf1-212"><xref:security/app-secrets>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-212">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="e6cf1-213">환경에 따른 시작 클래스 및 메서드</span><span class="sxs-lookup"><span data-stu-id="e6cf1-213">Environment-based Startup class and methods</span></span>

### <a name="startup-class-conventions"></a><span data-ttu-id="e6cf1-214">시작 클래스 규칙</span><span class="sxs-lookup"><span data-stu-id="e6cf1-214">Startup class conventions</span></span>

<span data-ttu-id="e6cf1-215">ASP.NET Core 앱이 시작되면 [시작 클래스](xref:fundamentals/startup)가 앱을 부트스트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-215">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="e6cf1-216">앱은 다양한 환경(예: `StartupDevelopment`)에 대한 별도의 `Startup` 클래스를 정의할 수 있으며 적절한 `Startup` 클래스는 런타임에 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-216">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`), and the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="e6cf1-217">해당 이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-217">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="e6cf1-218">일치하는 `Startup{EnvironmentName}` 클래스를 찾을 수 없으면 `Startup` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-218">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span>

<span data-ttu-id="e6cf1-219">환경 기반 `Startup` 클래스를 구현하려면 사용 중인 각 환경에 대한 `Startup{EnvironmentName}` 클래스와 폴백 `Startup` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-219">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

<span data-ttu-id="e6cf1-220">어셈블리 이름을 허용하는 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-220">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="e6cf1-221">시작 메서드 규칙</span><span class="sxs-lookup"><span data-stu-id="e6cf1-221">Startup method conventions</span></span>

<span data-ttu-id="e6cf1-222">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)는 `Configure<EnvironmentName>` 및 `Configure<EnvironmentName>Services` 양식의 환경 특정 버전을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e6cf1-222">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`:</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="e6cf1-223">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e6cf1-223">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="e6cf1-224">IHostingEnvironment.EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e6cf1-224">IHostingEnvironment.EnvironmentName</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
