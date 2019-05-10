---
title: ASP.NET Core 앱 배포용 Visual Studio 게시 프로필
author: rick-anderson
description: Visual Studio에서 게시 프로필을 만들고 다양한 대상에 대한 ASP.NET Core 앱 배포를 관리하는 데 사용하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: e1e8f99be18d6f395a146bda805f71c46cd0346d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889458"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="3cac3-103">ASP.NET Core 앱 배포용 Visual Studio 게시 프로필</span><span class="sxs-lookup"><span data-stu-id="3cac3-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="3cac3-104">작성자: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3cac3-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="3cac3-105">이 항목 1.1 버전의 경우 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필(버전 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/VS_Publish_Profiles_1.1.pdf)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-105">For the 1.1 version of this topic, download [Visual Studio publish profiles for ASP.NET Core app deployment (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/VS_Publish_Profiles_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="3cac3-106">이 문서에서는 Visual Studio 2017 이상을 사용하여 게시 프로필을 만들고 사용하는 방법에 초점을 맞춥니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-106">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="3cac3-107">Visual Studio로 만들어진 게시 프로필은 MSBuild 및 Visual Studio에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-107">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="3cac3-108">Azure에 게시에 관한 지침은 [Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 웹앱 게시](xref:tutorials/publish-to-azure-webapp-using-vs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-108">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="3cac3-109">다음 프로젝트 파일은 `dotnet new mvc` 명령을 사용하여 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-109">The following project file was created with the command `dotnet new mvc`:</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

<span data-ttu-id="3cac3-110">`<Project>` 요소의 `Sdk` 특성은 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-110">The `<Project>` element's `Sdk` attribute accomplishes the following tasks:</span></span>

* <span data-ttu-id="3cac3-111">시작 시 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props*에서 속성 파일을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-111">Imports the properties file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* at the beginning.</span></span>
* <span data-ttu-id="3cac3-112">종료 시 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*에서 대상 파일을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-112">Imports the targets file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* at the end.</span></span>

<span data-ttu-id="3cac3-113">`MSBuildSDKsPath`의 기본 위치(Visual Studio 2017 Enterprise 사용)는 *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-113">The default location for `MSBuildSDKsPath` (with Visual Studio 2017 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="3cac3-114">`Microsoft.NET.Sdk.Web` SDK는 다음에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-114">The `Microsoft.NET.Sdk.Web` SDK depends on:</span></span>

* <span data-ttu-id="3cac3-115">*Microsoft.NET.Sdk.Web.ProjectSystem*</span><span class="sxs-lookup"><span data-stu-id="3cac3-115">*Microsoft.NET.Sdk.Web.ProjectSystem*</span></span>
* <span data-ttu-id="3cac3-116">*Microsoft.NET.Sdk.Publish*</span><span class="sxs-lookup"><span data-stu-id="3cac3-116">*Microsoft.NET.Sdk.Publish*</span></span>

<span data-ttu-id="3cac3-117">이러한 항목은 다음 속성 및 대상을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-117">Which causes the following properties and targets to be imported:</span></span>

* <span data-ttu-id="3cac3-118">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props*</span><span class="sxs-lookup"><span data-stu-id="3cac3-118">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props*</span></span>
* <span data-ttu-id="3cac3-119">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets*</span><span class="sxs-lookup"><span data-stu-id="3cac3-119">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets*</span></span>
* <span data-ttu-id="3cac3-120">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props*</span><span class="sxs-lookup"><span data-stu-id="3cac3-120">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props*</span></span>
* <span data-ttu-id="3cac3-121">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets*</span><span class="sxs-lookup"><span data-stu-id="3cac3-121">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets*</span></span>

<span data-ttu-id="3cac3-122">게시 대상은 사용된 게시 방법에 따라 올바른 대상 집합을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-122">Publish targets import the right set of targets based on the publish method used.</span></span>

<span data-ttu-id="3cac3-123">MSBuild 또는 Visual Studio가 프로젝트를 로드하면 다음 높은 수준의 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-123">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="3cac3-124">프로젝트 빌드</span><span class="sxs-lookup"><span data-stu-id="3cac3-124">Build project</span></span>
* <span data-ttu-id="3cac3-125">게시할 파일 컴퓨팅</span><span class="sxs-lookup"><span data-stu-id="3cac3-125">Compute files to publish</span></span>
* <span data-ttu-id="3cac3-126">대상에 파일 게시</span><span class="sxs-lookup"><span data-stu-id="3cac3-126">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="3cac3-127">프로젝트 항목 컴퓨팅</span><span class="sxs-lookup"><span data-stu-id="3cac3-127">Compute project items</span></span>

<span data-ttu-id="3cac3-128">프로젝트가 로드되면 프로젝트 항목(파일)이 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-128">When the project is loaded, the project items (files) are computed.</span></span> <span data-ttu-id="3cac3-129">`item type` 특성에 따라 파일 처리 방법이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-129">The `item type` attribute determines how the file is processed.</span></span> <span data-ttu-id="3cac3-130">기본적으로 *.cs* 파일은 `Compile` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-130">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="3cac3-131">`Compile` 항목 목록의 파일이 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-131">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="3cac3-132">`Content` 항목 목록에는 빌드 출력 이외에 게시될 파일이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-132">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="3cac3-133">기본적으로 패턴 `wwwroot/**`와 일치하는 파일은 `Content` 항목에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-133">By default, files matching the pattern `wwwroot/**` are included in the `Content` item.</span></span> <span data-ttu-id="3cac3-134">[와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)인 `wwwroot/\*\*`는 *wwwroot* **및** 하위 폴더에서 모든 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-134">The `wwwroot/\*\*` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** subfolders.</span></span> <span data-ttu-id="3cac3-135">게시 목록에 파일을 명시적으로 추가하려면 [포함 파일](#include-files)에 표시된 대로 *.csproj* 파일에서 직접 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-135">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in [Include Files](#include-files).</span></span>

<span data-ttu-id="3cac3-136">Visual Studio에서 **게시** 단추를 선택하거나 명령줄에서 게시할 경우:</span><span class="sxs-lookup"><span data-stu-id="3cac3-136">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="3cac3-137">속성/항목이 계산됩니다(빌드하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="3cac3-137">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="3cac3-138">**Visual Studio 전용**: NuGet 패키지가 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-138">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="3cac3-139">(CLI에서 사용자가 명시적으로 복원해야 합니다.)</span><span class="sxs-lookup"><span data-stu-id="3cac3-139">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="3cac3-140">프로젝트가 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-140">The project builds.</span></span>
* <span data-ttu-id="3cac3-141">게시 항목이 계산됩니다(게시하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="3cac3-141">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="3cac3-142">프로젝트가 게시됩니다(계산된 파일이 게시 대상에 복사됨).</span><span class="sxs-lookup"><span data-stu-id="3cac3-142">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="3cac3-143">ASP.NET Core 프로젝트가 프로젝트 파일의 `Microsoft.NET.Sdk.Web`을 참조하면 *app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-143">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="3cac3-144">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-144">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="3cac3-145">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-145">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="3cac3-146">기본 명령줄 게시</span><span class="sxs-lookup"><span data-stu-id="3cac3-146">Basic command-line publishing</span></span>

<span data-ttu-id="3cac3-147">명령줄 게시는 모든 .NET Core 지원 플랫폼에 적용되며 Visual Studio가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-147">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="3cac3-148">아래 샘플에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 *.csproj* 파일이 포함된 프로젝트 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-148">In the samples below, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="3cac3-149">프로젝트 폴더에 없는 경우 프로젝트 파일 경로를 명시적으로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-149">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="3cac3-150">예:</span><span class="sxs-lookup"><span data-stu-id="3cac3-150">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="3cac3-151">다음 명령을 실행하여 웹앱을 만들고 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-151">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="3cac3-152">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 다음과 유사한 출력 결과를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-152">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

<span data-ttu-id="3cac3-153">기본 게시 폴더는 `bin\$(Configuration)\netcoreapp<version>\publish`입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-153">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="3cac3-154">`$(Configuration)`의 기본값은 *Debug*입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-154">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="3cac3-155">앞의 예제에서 `<TargetFramework>`는 `netcoreapp2.0`입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-155">In the preceding sample, the `<TargetFramework>` is `netcoreapp2.0`.</span></span>

<span data-ttu-id="3cac3-156">`dotnet publish -h`는 게시에 대한 도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-156">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="3cac3-157">다음 명령은 `Release` 빌드 및 게시 디렉터리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-157">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="3cac3-158">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 `Publish` 대상을 호출하는 MSBuild를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-158">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="3cac3-159">`dotnet publish` 및 MSBuild에 전달된 모든 매개 변수.</span><span class="sxs-lookup"><span data-stu-id="3cac3-159">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="3cac3-160">`-c` 매개 변수는 `Configuration` MSBuild 속성에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-160">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="3cac3-161">`-o` 매개 변수는 `OutputPath`에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-161">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="3cac3-162">다음 형식 중 하나를 사용하여 MSBuild 속성을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-162">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="3cac3-163">다음 명령은 `Release` 빌드를 네트워크 공유에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-163">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="3cac3-164">네트워크 공유는 슬래시(*//r8/*)를 사용하여 지정하고 모든 .NET Core 지원 플랫폼에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-164">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="3cac3-165">배포할 게시된 앱이 실행되고 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-165">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="3cac3-166">앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-166">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="3cac3-167">잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-167">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="3cac3-168">게시 프로필</span><span class="sxs-lookup"><span data-stu-id="3cac3-168">Publish profiles</span></span>

<span data-ttu-id="3cac3-169">이 섹션에서는 Visual Studio 2017 이상을 사용하여 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-169">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="3cac3-170">프로필이 만들어지면 Visual Studio 또는 명령줄에서 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-170">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="3cac3-171">게시 프로필을 사용하면 게시 프로세스를 간소화할 수 있고 제한 없이 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-171">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="3cac3-172">다음 경로 중 하나를 선택하여 Visual Studio에서 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-172">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="3cac3-173">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-173">Right-click the project in Solution Explorer and select **Publish**.</span></span>
* <span data-ttu-id="3cac3-174">**빌드** 메뉴에서 **게시 {PROJECT NAME}** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-174">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="3cac3-175">앱 기능 페이지의 **게시** 탭이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-175">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="3cac3-176">프로젝트에 게시 프로필이 없으면 다음 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-176">If the project lacks a publish profile, the following page is displayed:</span></span>

![앱 기능 페이지의 게시 탭](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="3cac3-178">**폴더**를 선택할 경우 게시된 자산을 저장할 폴더 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-178">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="3cac3-179">기본 폴더는 *bin\Release\PublishOutput*입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-179">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="3cac3-180">**프로필 만들기** 단추를 클릭하여 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-180">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="3cac3-181">게시 프로필이 만들어지면 **게시** 탭이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-181">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="3cac3-182">새로 만들어진 프로필이 드롭다운 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="3cac3-183">**새 프로필 만들기**를 클릭하여 또 다른 새 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-183">Click **Create new profile** to create another new profile.</span></span>

![FolderProfile을 표시하는 앱 기능 페이지의 게시 탭](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="3cac3-185">게시 마법사는 다음 게시 대상을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-185">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="3cac3-186">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3cac3-186">Azure App Service</span></span>
* <span data-ttu-id="3cac3-187">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="3cac3-187">Azure Virtual Machines</span></span>
* <span data-ttu-id="3cac3-188">IIS, FTP 등(웹 서버의 경우)</span><span class="sxs-lookup"><span data-stu-id="3cac3-188">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="3cac3-189">폴더</span><span class="sxs-lookup"><span data-stu-id="3cac3-189">Folder</span></span>
* <span data-ttu-id="3cac3-190">프로필 가져오기</span><span class="sxs-lookup"><span data-stu-id="3cac3-190">Import Profile</span></span>

<span data-ttu-id="3cac3-191">자세한 내용은 [내게 적합한 게시 옵션](/visualstudio/ide/not-in-toc/web-publish-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-191">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="3cac3-192">Visual Studio를 사용하여 게시 프로필을 만들면 *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-192">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="3cac3-193">이 *.pubxml* 파일은 MSBuild 파일이고 게시 구성 설정을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-193">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="3cac3-194">이 파일을 변경하여 빌드 및 게시 프로세스를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-194">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="3cac3-195">게시 프로세스에서 이 파일을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-195">This file is read by the publishing process.</span></span> <span data-ttu-id="3cac3-196">`<LastUsedBuildConfiguration>`은 전역 속성이고 빌드에서 가져온 파일에 포함되면 안 되므로 특별합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-196">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="3cac3-197">자세한 내용은 [MSBuild: 구성 속성을 설정하는 방법](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-197">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="3cac3-198">Azure 대상에 게시하는 경우 *.pubxml* 파일에는 Azure 구독 식별자가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-198">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="3cac3-199">대상 유형을 사용할 경우 이 파일을 소스 제어에 추가하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-199">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="3cac3-200">Azure 이외 대상에 게시하는 경우 *.pubxml* 파일을 체크 인해도 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-200">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="3cac3-201">중요 정보(예: 게시 암호)는 사용자/컴퓨터 수준별로 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-201">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="3cac3-202">이 정보는 *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-202">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="3cac3-203">중요 정보가 저장될 수 있는 파일이므로 소스 제어에 체크 인하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-203">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="3cac3-204">ASP.NET Core에서 웹앱을 게시하는 방법에 대한 개요는 [호스트 및 배포](xref:host-and-deploy/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-204">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="3cac3-205">ASP.NET Core 앱을 게시하는 데 필요한 MSBuild 작업 및 대상은 https://github.com/aspnet/websdk의 오픈 소스입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-205">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at https://github.com/aspnet/websdk.</span></span>

<span data-ttu-id="3cac3-206">`dotnet publish`는 MSDeploy 폴더와 [Kudu](https://github.com/projectkudu/kudu/wiki) 게시 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-206">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="3cac3-207">폴더(플랫폼 간에 작동):</span><span class="sxs-lookup"><span data-stu-id="3cac3-207">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="3cac3-208">MSDeploy(MSDeploy가 플랫폼 간이 아니기 때문에 현재 Windows에서만 작동):</span><span class="sxs-lookup"><span data-stu-id="3cac3-208">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="3cac3-209">MSDeploy 패키지(MSDeploy가 플랫폼 간이 아니기 때문에 현재 Windows에서만 작동):</span><span class="sxs-lookup"><span data-stu-id="3cac3-209">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="3cac3-210">위 샘플에서 `deployonbuild`를 `dotnet publish`에 전달하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="3cac3-210">In the preceding samples, **don't** pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="3cac3-211">자세한 내용은 [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-211">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="3cac3-212">`dotnet publish`는 모든 플랫폼에서 Azure에 게시하기 위해 Kudu API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-212">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="3cac3-213">Visual Studio 게시는 Kudu API를 지원하지만 플랫폼 간 Azure에 게시에 대해 WebSDK에 의해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-213">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="3cac3-214">다음 콘텐츠와 함께 *Properties/PublishProfiles* 폴더에 게시 프로필을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-214">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

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

<span data-ttu-id="3cac3-215">다음 명령을 실행하면 게시 콘텐츠가 압축되고 Kudu API를 사용하여 Azure에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-215">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="3cac3-216">게시 프로필을 사용할 경우 다음 MSBuild 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-216">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="3cac3-217">*FolderProfile*이라는 프로필을 사용하여 게시할 경우 다음 명령 중 하나를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-217">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="3cac3-218">[dotnet build](/dotnet/core/tools/dotnet-build)를 호출하면 이 명령은 `msbuild`를 호출하여 빌드 및 게시 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-218">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="3cac3-219">폴더 프로필을 전달할 경우 `dotnet build` 또는 `msbuild` 호출은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-219">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="3cac3-220">Windows에서 MSBuild를 직접 호출하면 MSBuild의 .NET Framework 버전이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-220">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="3cac3-221">MSDeploy는 현재 게시를 위해 Windows 컴퓨터로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-221">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="3cac3-222">폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고 MSBuild는 폴더가 아닌 프로필에서 MSDeploy를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-222">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="3cac3-223">폴더가 아닌 프로필에서 `dotnet build`를 호출하면 MSBuild가 호출되고(MSDeploy 사용) Windows 플랫폼에서 실행될 경우에도 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-223">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="3cac3-224">폴더가 아닌 프로필을 사용하여 게시하려면 MSBuild를 직접 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-224">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="3cac3-225">다음 폴더 게시 프로필은 Visual Studio를 사용하여 만들어졌고 네트워크 공유에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-225">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="3cac3-226">`<LastUsedBuildConfiguration>`은 `Release`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-226">Note `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="3cac3-227">Visual Studio에서 게시할 경우 `<LastUsedBuildConfiguration>` 구성 속성 값은 게시 프로세스가 시작될 때의 값을 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-227">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="3cac3-228">`<LastUsedBuildConfiguration>` 구성 속성은 특별하고 가져온 MSBuild 파일에서 재정의되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-228">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="3cac3-229">이 속성은 명령줄에서 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-229">This property can be overridden from the command line.</span></span>

<span data-ttu-id="3cac3-230">.NET Core CLI 사용:</span><span class="sxs-lookup"><span data-stu-id="3cac3-230">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="3cac3-231">MSBuild 사용:</span><span class="sxs-lookup"><span data-stu-id="3cac3-231">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="3cac3-232">명령줄에서 MSDeploy 엔드포인트에 게시</span><span class="sxs-lookup"><span data-stu-id="3cac3-232">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="3cac3-233">다음 예제에서는 *AzureWebApp*라는 Visual Studio에서 만든 ASP.NET Core 웹앱을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-233">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="3cac3-234">Azure 게시 프로필이 Visual Studio에 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-234">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="3cac3-235">프로필을 만드는 방법에 대한 자세한 내용은 [게시 프로필](#publish-profiles) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-235">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="3cac3-236">게시 프로필을 사용하여 앱을 배포하려면 Visual Studio **개발자 명령 프롬프트**에서 `msbuild` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-236">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="3cac3-237">명령 프롬프트는 Windows 작업 표시줄의 **시작** 메뉴에 있는 *Visual Studio* 폴더에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-237">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="3cac3-238">쉽게 액세스할 수 있도록 Visual Studio의 **도구** 메뉴에 명령 프롬프트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-238">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="3cac3-239">자세한 내용은 [Visual Studio용 개발자 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-239">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="3cac3-240">MSBuild는 다음 명령 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-240">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="3cac3-241">{PATH} &ndash; 앱의 프로젝트 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-241">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="3cac3-242">{PROFILE} &ndash; 게시 프로필의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-242">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="3cac3-243">{USERNAME} &ndash; MSDeploy 사용자 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-243">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="3cac3-244">{USERNAME}은 게시 프로필에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-244">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="3cac3-245">{PASSWORD} &ndash; MSDeploy 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-245">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="3cac3-246">*{PROFILE}.PublishSettings* 파일에서 {PASSWORD}를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-246">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="3cac3-247">다음 위치에서 *.PublishSettings* 파일을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-247">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="3cac3-248">솔루션 탐색기: **보기** > **클라우드 탐색기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-248">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="3cac3-249">Azure 구독으로 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-249">Connect with your Azure subscription.</span></span> <span data-ttu-id="3cac3-250">**App Services**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-250">Open **App Services**.</span></span> <span data-ttu-id="3cac3-251">앱을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-251">Right-click the app.</span></span> <span data-ttu-id="3cac3-252">**게시 프로필 다운로드**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-252">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="3cac3-253">Azure Portal: 웹앱의 **개요** 패널에서 **게시 프로필 가져오기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-253">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="3cac3-254">다음 예제에서는 *AzureWebApp - 웹 배포*라는 게시 프로필을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-254">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="3cac3-255">게시 프로필은 Windows 명령 프롬프트에서 .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령과 함께 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-255">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="3cac3-256">[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령은 플랫폼 간 사용할 수 있으며 macOS 및 Linux에서 ASP.NET Core 앱을 컴파일할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-256">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="3cac3-257">그러나 macOS 및 Linux의 MSBuild는 Azure 또는 다른 MSDeploy 앤드포인트에 앱을 배포할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-257">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="3cac3-258">MSDeploy는 Windows에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-258">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="3cac3-259">환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-259">Set the environment</span></span>

<span data-ttu-id="3cac3-260">`<EnvironmentName>` 속성을 게시 프로필(*.pubxml*) 또는 프로젝트 파일에 포함하여 앱의 [환경](xref:fundamentals/environments)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-260">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="3cac3-261">*web.config* 변환(예: 구성, 프로필 또는 환경에 따라 환경 변수 설정)이 필요한 경우 <xref:host-and-deploy/iis/transform-webconfig>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-261">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="3cac3-262">파일 제외</span><span class="sxs-lookup"><span data-stu-id="3cac3-262">Exclude files</span></span>

<span data-ttu-id="3cac3-263">ASP.NET Core 웹앱을 게시할 경우 *wwwroot* 폴더의 빌드 아티팩트 및 콘텐츠가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-263">When publishing ASP.NET Core web apps, the build artifacts and contents of the *wwwroot* folder are included.</span></span> <span data-ttu-id="3cac3-264">`msbuild`는 [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-264">`msbuild` supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="3cac3-265">예를 들어 다음 `<Content>` 요소는 *wwwroot/content* 폴더와 모든 하위 폴더에서 모든 텍스트(*.txt*) 파일을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-265">For example, the following `<Content>` element excludes all text (*.txt*) files from the *wwwroot/content* folder and all its subfolders.</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="3cac3-266">앞의 태그는 게시 프로필 또는 *.csproj* 파일에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-266">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="3cac3-267">*.csproj* 파일에 추가된 규칙은 프로젝트의 모든 게시 프로필에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-267">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="3cac3-268">다음 `<MsDeploySkipRules>` 요소는 *wwwroot/content* 폴더에서 모든 파일을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-268">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot/content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="3cac3-269">`<MsDeploySkipRules>`는 배포 사이트에서 ‘건너뛰기’ 대상을 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-269">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="3cac3-270">`<Content>` 대상 파일 및 폴더는 배포 사이트에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-270">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="3cac3-271">예를 들어 배포된 웹앱에 다음 파일이 포함되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-271">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="3cac3-272">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3cac3-272">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="3cac3-273">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3cac3-273">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="3cac3-274">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3cac3-274">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="3cac3-275">다음 `<MsDeploySkipRules>` 요소가 추가되면 해당 파일은 배포 사이트에서 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-275">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="3cac3-276">앞의 `<MsDeploySkipRules>` 요소는 ‘건너뛴’ 파일이 배포되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-276">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="3cac3-277">해당 파일은 배포된 후에 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-277">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="3cac3-278">다음 `<Content>` 요소는 배포 사이트에서 대상 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-278">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="3cac3-279">앞의 `<Content>` 요소와 함께 명령줄 배포를 사용하면 다음 출력이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-279">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="3cac3-280">포함 파일</span><span class="sxs-lookup"><span data-stu-id="3cac3-280">Include files</span></span>

<span data-ttu-id="3cac3-281">다음 태그는 게시 사이트의 *wwwroot/images* 폴더에 대한 프로젝트 디렉터리 외부에 *images* 폴더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-281">The following markup includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site:</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="3cac3-282">태그는 *.csproj* 파일 또는 게시 프로필에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-282">The markup can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="3cac3-283">태그가 *.csproj* 파일에 추가되면 프로젝트의 각 게시 프로필에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-283">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

<span data-ttu-id="3cac3-284">다음 강조 표시된 태그는 방법을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-284">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="3cac3-285">프로젝트 외부에서 *wwwroot* 폴더로 파일을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-285">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="3cac3-286">*wwwroot\Content* 폴더를 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-286">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="3cac3-287">*Views\Home\About2.cshtml*을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-287">Exclude *Views\Home\About2.cshtml*.</span></span>

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

<span data-ttu-id="3cac3-288">더 많은 배포 샘플을 보려면 [WebSDK Readme](https://github.com/aspnet/websdk)(WebSDK 추가 정보)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3cac3-288">See the [WebSDK Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="3cac3-289">게시 이전 또는 이후 대상 실행</span><span class="sxs-lookup"><span data-stu-id="3cac3-289">Run a target before or after publishing</span></span>

<span data-ttu-id="3cac3-290">기본 제공 `BeforePublish` 및 `AfterPublish` 대상은 게시 대상 이전 또는 이후에 대상을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-290">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="3cac3-291">다음 요소를 게시 프로필에 추가하여 게시 이전 및 이후에 콘솔 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-291">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="3cac3-292">신뢰할 수 없는 인증서를 사용하여 서버에 게시</span><span class="sxs-lookup"><span data-stu-id="3cac3-292">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="3cac3-293">`True` 값을 사용하는 `<AllowUntrustedCertificate>` 속성을 게시 프로필에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-293">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="3cac3-294">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="3cac3-294">The Kudu service</span></span>

<span data-ttu-id="3cac3-295">Azure App Service 웹앱 배포에 있는 파일을 보려면 [Kudu 서비스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-295">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="3cac3-296">`scm` 토큰을 웹앱 이름에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-296">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="3cac3-297">예:</span><span class="sxs-lookup"><span data-stu-id="3cac3-297">For example:</span></span>

| <span data-ttu-id="3cac3-298">URL</span><span class="sxs-lookup"><span data-stu-id="3cac3-298">URL</span></span>                                    | <span data-ttu-id="3cac3-299">결과</span><span class="sxs-lookup"><span data-stu-id="3cac3-299">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="3cac3-300">웹앱</span><span class="sxs-lookup"><span data-stu-id="3cac3-300">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="3cac3-301">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="3cac3-301">Kudu service</span></span> |

<span data-ttu-id="3cac3-302">[디버그 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 메뉴 항목을 선택하여 파일을 표시, 편집, 삭제 또는 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-302">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3cac3-303">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3cac3-303">Additional resources</span></span>

* <span data-ttu-id="3cac3-304">[웹 배포](https://www.iis.net/downloads/microsoft/web-deploy)(MSDeploy)는 IIS 서버에 대한 웹앱 및 웹 사이트 배포를 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="3cac3-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="3cac3-305">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): 배포에 대한 파일 문제 및 요청 기능.</span><span class="sxs-lookup"><span data-stu-id="3cac3-305">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="3cac3-306">Visual Studio에서 Azure VM에 ASP.NET 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="3cac3-306">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
