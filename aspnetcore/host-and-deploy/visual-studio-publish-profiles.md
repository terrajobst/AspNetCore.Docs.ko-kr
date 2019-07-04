---
title: ASP.NET Core 앱 배포용 Visual Studio 게시 프로필
author: rick-anderson
description: Visual Studio에서 게시 프로필을 만들고 다양한 대상에 대한 ASP.NET Core 앱 배포를 관리하는 데 사용하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: ac243a3898553b2e14a6c15d311afaf62f112a24
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207820"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="4d104-103">ASP.NET Core 앱 배포용 Visual Studio 게시 프로필</span><span class="sxs-lookup"><span data-stu-id="4d104-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="4d104-104">작성자: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d104-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4d104-105">이 문서에서는 Visual Studio 2017 이상을 사용하여 게시 프로필을 만들고 사용하는 방법에 초점을 맞춥니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-105">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="4d104-106">Visual Studio로 만들어진 게시 프로필은 MSBuild 및 Visual Studio에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-106">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="4d104-107">Azure에 게시에 관한 지침은 [Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 웹앱 게시](xref:tutorials/publish-to-azure-webapp-using-vs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-107">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="4d104-108">`dotnet new mvc` 명령은 다음 최상위 `<Project>` 요소가 포함된 프로젝트 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-108">The `dotnet new mvc` command produces a project file containing the following top-level `<Project>` element:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="4d104-109">위 `<Project>` 요소의 `Sdk` 특성은 각각 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* 및 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*에서 MSBuild [속성](/visualstudio/msbuild/msbuild-properties) 및 [대상](/visualstudio/msbuild/msbuild-targets)을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="4d104-110">`$(MSBuildSDKsPath)`(Visual Studio 2019 Enterprise 사용)의 기본 위치는 *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="4d104-111">`Microsoft.NET.Sdk.Web`(웹 SDK)은 `Microsoft.NET.Sdk`(.NET Core SDK) 및 `Microsoft.NET.Sdk.Razor`([Razor SDK](xref:razor-pages/sdk))를 비롯한 다른 SDK에 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="4d104-112">각 종속 SDK와 연결된 MSBuild 속성과 대상을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="4d104-113">게시 대상은 사용된 게시 방법에 따라 해당 대상 집합을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="4d104-114">MSBuild 또는 Visual Studio가 프로젝트를 로드하면 다음 높은 수준의 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="4d104-115">프로젝트 빌드</span><span class="sxs-lookup"><span data-stu-id="4d104-115">Build project</span></span>
* <span data-ttu-id="4d104-116">게시할 파일 컴퓨팅</span><span class="sxs-lookup"><span data-stu-id="4d104-116">Compute files to publish</span></span>
* <span data-ttu-id="4d104-117">대상에 파일 게시</span><span class="sxs-lookup"><span data-stu-id="4d104-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="4d104-118">프로젝트 항목 컴퓨팅</span><span class="sxs-lookup"><span data-stu-id="4d104-118">Compute project items</span></span>

<span data-ttu-id="4d104-119">프로젝트가 로드되면 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)(파일)이 컴퓨팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="4d104-120">항목 종류에 따라 파일 처리 방법이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="4d104-121">기본적으로 *.cs* 파일은 `Compile` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="4d104-122">`Compile` 항목 목록의 파일이 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="4d104-123">`Content` 항목 목록에는 빌드 출력 이외에 게시될 파일이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="4d104-124">기본적으로 `wwwroot\**`, `**\*.json` 및 `Content` 패턴과 일치하는 파일이 `**\*.config` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="4d104-125">예를 들어 `wwwroot\**` [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)은 *wwwroot* 폴더 **및** 하위 폴더의 모든 파일과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d104-126">웹 SDK는 [Razor SDK](xref:razor-pages/sdk)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="4d104-127">따라서 `**\*.cshtml` 및 `**\*.razor` 패턴과 일치하는 파일도 `Content` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="4d104-128">웹 SDK는 [Razor SDK](xref:razor-pages/sdk)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="4d104-129">따라서 `**\*.cshtml` 패턴과 일치하는 파일도 `Content` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="4d104-130">게시 목록에 파일을 명시적으로 추가하려면 [포함 파일](#include-files) 섹션에 표시된 대로 *.csproj* 파일에서 직접 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="4d104-131">Visual Studio에서 **게시** 단추를 선택하거나 명령줄에서 게시할 경우:</span><span class="sxs-lookup"><span data-stu-id="4d104-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="4d104-132">속성/항목이 계산됩니다(빌드하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="4d104-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="4d104-133">**Visual Studio 전용**: NuGet 패키지가 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="4d104-134">(CLI에서 사용자가 명시적으로 복원해야 합니다.)</span><span class="sxs-lookup"><span data-stu-id="4d104-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="4d104-135">프로젝트가 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-135">The project builds.</span></span>
* <span data-ttu-id="4d104-136">게시 항목이 계산됩니다(게시하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="4d104-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="4d104-137">프로젝트가 게시됩니다(계산된 파일이 게시 대상에 복사됨).</span><span class="sxs-lookup"><span data-stu-id="4d104-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="4d104-138">ASP.NET Core 프로젝트가 프로젝트 파일의 `Microsoft.NET.Sdk.Web`을 참조하면 *app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="4d104-139">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="4d104-140">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="4d104-141">기본 명령줄 게시</span><span class="sxs-lookup"><span data-stu-id="4d104-141">Basic command-line publishing</span></span>

<span data-ttu-id="4d104-142">명령줄 게시는 모든 .NET Core 지원 플랫폼에 적용되며 Visual Studio가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="4d104-143">다음 샘플에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 *.csproj* 파일이 포함된 프로젝트 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-143">In the following samples, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="4d104-144">프로젝트 폴더에 없는 경우 프로젝트 파일 경로를 명시적으로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-144">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="4d104-145">예:</span><span class="sxs-lookup"><span data-stu-id="4d104-145">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="4d104-146">다음 명령을 실행하여 웹앱을 만들고 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-146">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="4d104-147">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 다음과 유사한 출력 결과를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-147">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {version} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\publish\
```

<span data-ttu-id="4d104-148">기본 게시 폴더는 `bin\$(Configuration)\netcoreapp<version>\publish`입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-148">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="4d104-149">`$(Configuration)`의 기본값은 *Debug*입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-149">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="4d104-150">앞의 예제에서 `<TargetFramework>`는 `netcoreapp{X.Y}`입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-150">In the preceding sample, the `<TargetFramework>` is `netcoreapp{X.Y}`.</span></span>

<span data-ttu-id="4d104-151">`dotnet publish -h`는 게시에 대한 도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-151">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="4d104-152">다음 명령은 `Release` 빌드 및 게시 디렉터리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-152">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="4d104-153">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 `Publish` 대상을 호출하는 MSBuild를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-153">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="4d104-154">`dotnet publish` 및 MSBuild에 전달된 모든 매개 변수.</span><span class="sxs-lookup"><span data-stu-id="4d104-154">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="4d104-155">`-c` 매개 변수는 `Configuration` MSBuild 속성에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-155">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="4d104-156">`-o` 매개 변수는 `OutputPath`에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-156">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="4d104-157">다음 형식 중 하나를 사용하여 MSBuild 속성을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-157">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="4d104-158">다음 명령은 `Release` 빌드를 네트워크 공유에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-158">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="4d104-159">네트워크 공유는 슬래시( *//r8/* )를 사용하여 지정하고 모든 .NET Core 지원 플랫폼에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-159">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="4d104-160">배포할 게시된 앱이 실행되고 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-160">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="4d104-161">앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-161">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="4d104-162">잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-162">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="4d104-163">게시 프로필</span><span class="sxs-lookup"><span data-stu-id="4d104-163">Publish profiles</span></span>

<span data-ttu-id="4d104-164">이 섹션에서는 Visual Studio 2017 이상을 사용하여 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-164">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="4d104-165">프로필이 만들어지면 Visual Studio 또는 명령줄에서 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-165">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="4d104-166">게시 프로필을 사용하면 게시 프로세스를 간소화할 수 있고 제한 없이 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-166">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="4d104-167">다음 경로 중 하나를 선택하여 Visual Studio에서 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-167">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="4d104-168">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-168">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="4d104-169">**빌드** 메뉴에서 **게시 {PROJECT NAME}** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-169">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="4d104-170">앱 기능 페이지의 **게시** 탭이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-170">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="4d104-171">프로젝트에 게시 프로필이 없으면 다음 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-171">If the project lacks a publish profile, the following page is displayed:</span></span>

![앱 기능 페이지의 게시 탭](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="4d104-173">**폴더**를 선택할 경우 게시된 자산을 저장할 폴더 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-173">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="4d104-174">기본 폴더는 *bin\Release\PublishOutput*입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-174">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="4d104-175">**프로필 만들기** 단추를 클릭하여 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-175">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="4d104-176">게시 프로필이 만들어지면 **게시** 탭이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-176">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="4d104-177">새로 만들어진 프로필이 드롭다운 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-177">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="4d104-178">**새 프로필 만들기**를 클릭하여 또 다른 새 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-178">Click **Create new profile** to create another new profile.</span></span>

![FolderProfile을 표시하는 앱 기능 페이지의 게시 탭](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="4d104-180">게시 마법사는 다음 게시 대상을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-180">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="4d104-181">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4d104-181">Azure App Service</span></span>
* <span data-ttu-id="4d104-182">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="4d104-182">Azure Virtual Machines</span></span>
* <span data-ttu-id="4d104-183">IIS, FTP 등(웹 서버의 경우)</span><span class="sxs-lookup"><span data-stu-id="4d104-183">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="4d104-184">폴더</span><span class="sxs-lookup"><span data-stu-id="4d104-184">Folder</span></span>
* <span data-ttu-id="4d104-185">프로필 가져오기</span><span class="sxs-lookup"><span data-stu-id="4d104-185">Import Profile</span></span>

<span data-ttu-id="4d104-186">자세한 내용은 [내게 적합한 게시 옵션](/visualstudio/ide/not-in-toc/web-publish-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-186">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="4d104-187">Visual Studio를 사용하여 게시 프로필을 만들면 *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-187">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="4d104-188">이 *.pubxml* 파일은 MSBuild 파일이고 게시 구성 설정을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-188">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="4d104-189">이 파일을 변경하여 빌드 및 게시 프로세스를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-189">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="4d104-190">게시 프로세스에서 이 파일을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-190">This file is read by the publishing process.</span></span> <span data-ttu-id="4d104-191">`<LastUsedBuildConfiguration>`은 전역 속성이고 빌드에서 가져온 파일에 포함되면 안 되므로 특별합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-191">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="4d104-192">자세한 내용은 [MSBuild: 구성 속성을 설정하는 방법](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-192">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="4d104-193">Azure 대상에 게시하는 경우 *.pubxml* 파일에는 Azure 구독 식별자가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-193">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="4d104-194">대상 유형을 사용할 경우 이 파일을 소스 제어에 추가하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-194">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="4d104-195">Azure 이외 대상에 게시하는 경우 *.pubxml* 파일을 체크 인해도 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-195">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="4d104-196">중요 정보(예: 게시 암호)는 사용자/컴퓨터 수준별로 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-196">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="4d104-197">이 정보는 *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-197">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="4d104-198">중요 정보가 저장될 수 있는 파일이므로 소스 제어에 체크 인하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-198">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="4d104-199">ASP.NET Core에서 웹앱을 게시하는 방법에 대한 개요는 [호스트 및 배포](xref:host-and-deploy/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-199">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="4d104-200">ASP.NET Core 앱을 게시하는 데 필요한 MSBuild 작업 및 대상은 [aspnet/websdk 리포지토리](https://github.com/aspnet/websdk)에 있는 오픈 소스입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-200">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="4d104-201">`dotnet publish`는 MSDeploy 폴더와 [Kudu](https://github.com/projectkudu/kudu/wiki) 게시 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-201">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="4d104-202">폴더(플랫폼 간에 작동):</span><span class="sxs-lookup"><span data-stu-id="4d104-202">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="4d104-203">MSDeploy(MSDeploy가 플랫폼 간이 아니기 때문에 현재 Windows에서만 작동):</span><span class="sxs-lookup"><span data-stu-id="4d104-203">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="4d104-204">MSDeploy 패키지(MSDeploy가 플랫폼 간이 아니기 때문에 현재 Windows에서만 작동):</span><span class="sxs-lookup"><span data-stu-id="4d104-204">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="4d104-205">위 예제에서 `deployonbuild`를 `dotnet publish`에 전달하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-205">In the preceding examples, don't pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="4d104-206">자세한 내용은 [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-206">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="4d104-207">`dotnet publish`는 모든 플랫폼에서 Azure에 게시하기 위해 Kudu API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-207">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="4d104-208">Visual Studio 게시는 Kudu API를 지원하지만 플랫폼 간 Azure에 게시에 대해 WebSDK에 의해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-208">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="4d104-209">다음 콘텐츠와 함께 *Properties/PublishProfiles* 폴더에 게시 프로필을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-209">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="4d104-210">다음 명령을 실행하면 게시 콘텐츠가 압축되고 Kudu API를 사용하여 Azure에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-210">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="4d104-211">게시 프로필을 사용할 경우 다음 MSBuild 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-211">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="4d104-212">*FolderProfile*이라는 프로필을 사용하여 게시할 경우 다음 명령 중 하나를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-212">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="4d104-213">[dotnet build](/dotnet/core/tools/dotnet-build)를 호출하면 이 명령은 `msbuild`를 호출하여 빌드 및 게시 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-213">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="4d104-214">폴더 프로필을 전달할 경우 `dotnet build` 또는 `msbuild` 호출은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-214">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="4d104-215">Windows에서 MSBuild를 직접 호출하면 MSBuild의 .NET Framework 버전이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-215">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="4d104-216">MSDeploy는 현재 게시를 위해 Windows 컴퓨터로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-216">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="4d104-217">폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고 MSBuild는 폴더가 아닌 프로필에서 MSDeploy를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-217">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="4d104-218">폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고(MSDeploy 사용) Windows 플랫폼에서 실행될 경우에도 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-218">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="4d104-219">폴더가 아닌 프로필을 사용하여 게시하려면 MSBuild를 직접 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-219">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="4d104-220">다음 폴더 게시 프로필은 Visual Studio를 사용하여 만들어졌고 네트워크 공유에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="4d104-221">위 예제에서는 `<LastUsedBuildConfiguration>`이 `Release`로 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-221">In the preceding example, `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="4d104-222">Visual Studio에서 게시할 경우 `<LastUsedBuildConfiguration>` 구성 속성 값은 게시 프로세스가 시작될 때의 값을 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-222">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="4d104-223">`<LastUsedBuildConfiguration>` 구성 속성은 특별하고 가져온 MSBuild 파일에서 재정의되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-223">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="4d104-224">이 속성은 명령줄에서 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-224">This property can be overridden from the command line.</span></span>

<span data-ttu-id="4d104-225">.NET Core CLI 사용:</span><span class="sxs-lookup"><span data-stu-id="4d104-225">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="4d104-226">MSBuild 사용:</span><span class="sxs-lookup"><span data-stu-id="4d104-226">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="4d104-227">명령줄에서 MSDeploy 엔드포인트에 게시</span><span class="sxs-lookup"><span data-stu-id="4d104-227">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="4d104-228">다음 예제에서는 *AzureWebApp*라는 Visual Studio에서 만든 ASP.NET Core 웹앱을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-228">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="4d104-229">Azure 게시 프로필이 Visual Studio에 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-229">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="4d104-230">프로필을 만드는 방법에 대한 자세한 내용은 [게시 프로필](#publish-profiles) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-230">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="4d104-231">게시 프로필을 사용하여 앱을 배포하려면 Visual Studio **개발자 명령 프롬프트**에서 `msbuild` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-231">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="4d104-232">명령 프롬프트는 Windows 작업 표시줄의 **시작** 메뉴에 있는 *Visual Studio* 폴더에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-232">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="4d104-233">쉽게 액세스할 수 있도록 Visual Studio의 **도구** 메뉴에 명령 프롬프트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-233">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="4d104-234">자세한 내용은 [Visual Studio용 개발자 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-234">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="4d104-235">MSBuild는 다음 명령 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-235">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="4d104-236">{PATH} &ndash; 앱의 프로젝트 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-236">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="4d104-237">{PROFILE} &ndash; 게시 프로필의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-237">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="4d104-238">{USERNAME} &ndash; MSDeploy 사용자 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-238">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="4d104-239">{USERNAME}은 게시 프로필에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-239">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="4d104-240">{PASSWORD} &ndash; MSDeploy 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-240">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="4d104-241">*{PROFILE}.PublishSettings* 파일에서 {PASSWORD}를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-241">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="4d104-242">다음 위치에서 *.PublishSettings* 파일을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-242">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="4d104-243">솔루션 탐색기: **보기** > **클라우드 탐색기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-243">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="4d104-244">Azure 구독으로 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-244">Connect with your Azure subscription.</span></span> <span data-ttu-id="4d104-245">**App Services**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-245">Open **App Services**.</span></span> <span data-ttu-id="4d104-246">앱을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-246">Right-click the app.</span></span> <span data-ttu-id="4d104-247">**게시 프로필 다운로드**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-247">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="4d104-248">Azure Portal: 웹앱의 **개요** 패널에서 **게시 프로필 가져오기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-248">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="4d104-249">다음 예제에서는 *AzureWebApp - 웹 배포*라는 게시 프로필을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-249">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="4d104-250">게시 프로필은 Windows 명령 프롬프트에서 .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령과 함께 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-250">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="4d104-251">[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령은 플랫폼 간 사용할 수 있으며 macOS 및 Linux에서 ASP.NET Core 앱을 컴파일할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-251">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="4d104-252">그러나 macOS 및 Linux의 MSBuild는 Azure 또는 다른 MSDeploy 앤드포인트에 앱을 배포할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-252">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="4d104-253">MSDeploy는 Windows에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-253">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="4d104-254">환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-254">Set the environment</span></span>

<span data-ttu-id="4d104-255">`<EnvironmentName>` 속성을 게시 프로필( *.pubxml*) 또는 프로젝트 파일에 포함하여 앱의 [환경](xref:fundamentals/environments)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="4d104-256">*web.config* 변환(예: 구성, 프로필 또는 환경에 따라 환경 변수 설정)이 필요한 경우 <xref:host-and-deploy/iis/transform-webconfig>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="4d104-257">파일 제외</span><span class="sxs-lookup"><span data-stu-id="4d104-257">Exclude files</span></span>

<span data-ttu-id="4d104-258">ASP.NET Core 웹앱을 게시하는 경우 다음 자산이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="4d104-259">빌드 아티팩트</span><span class="sxs-lookup"><span data-stu-id="4d104-259">Build artifacts</span></span>
* <span data-ttu-id="4d104-260">다음 와일드카드 사용 패턴과 일치하는 폴더 및 파일:</span><span class="sxs-lookup"><span data-stu-id="4d104-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="4d104-261">`**\*.config`(예: *web.config*)</span><span class="sxs-lookup"><span data-stu-id="4d104-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="4d104-262">`**\*.json`(예: *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="4d104-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="4d104-263">MSBuild는 [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="4d104-264">예를 들어 다음 `<Content>` 요소는 *wwwroot\content* 폴더와 모든 하위 폴더에 있는 텍스트( *.txt*) 파일의 복사를 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="4d104-265">앞의 태그는 게시 프로필 또는 *.csproj* 파일에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="4d104-266">*.csproj* 파일에 추가된 규칙은 프로젝트의 모든 게시 프로필에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="4d104-267">다음 `<MsDeploySkipRules>` 요소는 *wwwroot\content* 폴더의 모든 파일을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="4d104-268">`<MsDeploySkipRules>`는 배포 사이트에서 ‘건너뛰기’ 대상을 삭제하지 않습니다. </span><span class="sxs-lookup"><span data-stu-id="4d104-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="4d104-269">`<Content>` 대상 파일 및 폴더는 배포 사이트에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="4d104-270">예를 들어 배포된 웹앱에 다음 파일이 포함되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="4d104-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4d104-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="4d104-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4d104-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="4d104-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4d104-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="4d104-274">다음 `<MsDeploySkipRules>` 요소가 추가되면 해당 파일은 배포 사이트에서 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="4d104-275">앞의 `<MsDeploySkipRules>` 요소는 ‘건너뛴’ 파일이 배포되지 않도록 합니다. </span><span class="sxs-lookup"><span data-stu-id="4d104-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="4d104-276">해당 파일은 배포된 후에 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="4d104-277">다음 `<Content>` 요소는 배포 사이트에서 대상 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="4d104-278">앞의 `<Content>` 요소와 함께 명령줄 배포를 사용하면 다음 출력이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-278">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="4d104-279">포함 파일</span><span class="sxs-lookup"><span data-stu-id="4d104-279">Include files</span></span>

<span data-ttu-id="4d104-280">다음 태그를 살펴보시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-280">The following markup:</span></span>

* <span data-ttu-id="4d104-281">프로젝트 디렉터리 외부의 *images* 폴더를 게시 사이트의 *wwwroot/images* 폴더에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-281">Includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>
* <span data-ttu-id="4d104-282">*.csproj* 파일 또는 게시 프로필에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-282">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="4d104-283">태그가 *.csproj* 파일에 추가되면 프로젝트의 각 게시 프로필에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-283">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="4d104-284">다음 강조 표시된 태그는 방법을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-284">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="4d104-285">프로젝트 외부에서 *wwwroot* 폴더로 파일을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-285">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="4d104-286">*wwwroot\Content* 폴더를 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-286">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="4d104-287">*Views\Home\About2.cshtml*을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-287">Exclude *Views\Home\About2.cshtml*.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

<span data-ttu-id="4d104-288">더 많은 배포 샘플을 보려면 [웹 SDK 리포지토리 추가 정보](https://github.com/aspnet/websdk)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d104-288">See the [Web SDK repository Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="4d104-289">게시 이전 또는 이후 대상 실행</span><span class="sxs-lookup"><span data-stu-id="4d104-289">Run a target before or after publishing</span></span>

<span data-ttu-id="4d104-290">기본 제공 `BeforePublish` 및 `AfterPublish` 대상은 게시 대상 이전 또는 이후에 대상을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-290">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="4d104-291">다음 요소를 게시 프로필에 추가하여 게시 이전 및 이후에 콘솔 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-291">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="4d104-292">신뢰할 수 없는 인증서를 사용하여 서버에 게시</span><span class="sxs-lookup"><span data-stu-id="4d104-292">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="4d104-293">`True` 값을 사용하는 `<AllowUntrustedCertificate>` 속성을 게시 프로필에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-293">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="4d104-294">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="4d104-294">The Kudu service</span></span>

<span data-ttu-id="4d104-295">Azure App Service 웹앱 배포에 있는 파일을 보려면 [Kudu 서비스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-295">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="4d104-296">`scm` 토큰을 웹앱 이름에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-296">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="4d104-297">예:</span><span class="sxs-lookup"><span data-stu-id="4d104-297">For example:</span></span>

| <span data-ttu-id="4d104-298">URL</span><span class="sxs-lookup"><span data-stu-id="4d104-298">URL</span></span>                                    | <span data-ttu-id="4d104-299">결과</span><span class="sxs-lookup"><span data-stu-id="4d104-299">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="4d104-300">웹앱</span><span class="sxs-lookup"><span data-stu-id="4d104-300">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="4d104-301">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="4d104-301">Kudu service</span></span> |

<span data-ttu-id="4d104-302">[디버그 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 메뉴 항목을 선택하여 파일을 표시, 편집, 삭제 또는 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-302">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d104-303">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4d104-303">Additional resources</span></span>

* <span data-ttu-id="4d104-304">[웹 배포](https://www.iis.net/downloads/microsoft/web-deploy)(MSDeploy)는 IIS 서버에 대한 웹앱 및 웹 사이트 배포를 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="4d104-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="4d104-305">[웹 SDK GitHub 리포지토리](https://github.com/aspnet/websdk/issues): 배포에 대한 파일 문제 및 요청 기능.</span><span class="sxs-lookup"><span data-stu-id="4d104-305">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="4d104-306">Visual Studio에서 Azure VM에 ASP.NET 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="4d104-306">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
