---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor 페이지를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/25/2018
uid: razor-pages/sdk
ms.openlocfilehash: de51c9443e639cd64c234b6975cf7252bb7a2b9a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895300"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="c70a9-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="c70a9-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="c70a9-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c70a9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c70a9-105">합니다 [!INCLUDE[](~/includes/2.1-SDK.md)] 포함 된 `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="c70a9-105">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="c70a9-106">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="c70a9-106">The Razor SDK:</span></span>

* <span data-ttu-id="c70a9-107">ASP.NET Core MVC 기반 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트의 빌드, 패키징 및 게시와 관련된 환경을 표준화합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-107">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="c70a9-108">Razor 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 집합이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-108">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c70a9-109">전제 조건</span><span class="sxs-lookup"><span data-stu-id="c70a9-109">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="using-the-razor-sdk"></a><span data-ttu-id="c70a9-110">Razor SDK 사용</span><span class="sxs-lookup"><span data-stu-id="c70a9-110">Using the Razor SDK</span></span>

<span data-ttu-id="c70a9-111">대부분의 웹 앱을 명시적으로 Razor SDK를 참조할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-111">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

<span data-ttu-id="c70a9-112">Razor SDK를 사용하여 Razor 보기 또는 Razor 페이지를 포함하는 클래스 라이브러리를 빌드하려면:</span><span class="sxs-lookup"><span data-stu-id="c70a9-112">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="c70a9-113">`Microsoft.NET.Sdk` 대신 `Microsoft.NET.Sdk.Razor`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-113">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    ...
  </Project>
  ```

* <span data-ttu-id="c70a9-114">일반적으로 패키지에 대 한 참조 `Microsoft.AspNetCore.Mvc` Razor 페이지 및 Razor 뷰 컴파일 및 빌드하는 데 필요한 추가 종속성을 수신 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-114">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="c70a9-115">최소한 프로젝트에 대 한 패키지 참조를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-115">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="c70a9-116">`Microsoft.AspNetCore.Razor.Design` 패키지를 프로젝트에 대 한 Razor 컴파일 작업 및 대상에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-116">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="c70a9-117">이전 패키지는 `Microsoft.AspNetCore.Mvc`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-117">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="c70a9-118">다음 태그는 ASP.NET Core Razor 페이지 앱에 대 한 Razor 파일 작성 하려면 Razor SDK를 사용 하는 프로젝트 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-118">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="c70a9-119">`Microsoft.AspNetCore.Razor.Design` 하 고 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 패키지에 포함 된 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-119">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c70a9-120">그러나 버전-작음 `Microsoft.AspNetCore.App` 패키지 참조는 메타 패키지의 최신 버전을 포함 하지 않는 앱에 제공 `Microsoft.AspNetCore.Razor.Design`합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-120">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="c70a9-121">프로젝트의 일관 된 버전을 참조 해야 합니다 `Microsoft.AspNetCore.Razor.Design` (또는 `Microsoft.AspNetCore.Mvc`) Razor에 대 한 최신 빌드 시간 수정 프로그램이 포함 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-121">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="c70a9-122">자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/Razor/issues/2553)합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-122">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="c70a9-123">속성</span><span class="sxs-lookup"><span data-stu-id="c70a9-123">Properties</span></span>

<span data-ttu-id="c70a9-124">다음 속성은 Razor의 SDK 동작을 프로젝트 빌드의 일부로 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-124">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="c70a9-125">`RazorCompileOnBuild` &ndash; 때 `true`, 컴파일하고 프로젝트 빌드의 일부로 Razor 어셈블리를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-125">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="c70a9-126">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-126">Defaults to `true`.</span></span>
* <span data-ttu-id="c70a9-127">`RazorCompileOnPublish` &ndash; 때 `true`, 컴파일 및 Razor 어셈블리 프로젝트를 게시 하는 일환으로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-127">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="c70a9-128">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-128">Defaults to `true`.</span></span>

<span data-ttu-id="c70a9-129">속성 및 다음 표에 항목을 입력 및 출력 Razor SDK를 구성 하려면 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-129">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

| <span data-ttu-id="c70a9-130">항목</span><span class="sxs-lookup"><span data-stu-id="c70a9-130">Items</span></span> | <span data-ttu-id="c70a9-131">설명</span><span class="sxs-lookup"><span data-stu-id="c70a9-131">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="c70a9-132">코드 생성 대상에 입력되는 항목 요소(*.cshtml* 파일).</span><span class="sxs-lookup"><span data-stu-id="c70a9-132">Item elements (*.cshtml* files) that are inputs to code generation targets.</span></span> |
| `RazorCompile` | <span data-ttu-id="c70a9-133">항목 요소 (*.cs* 파일)는 Razor 컴파일 대상에 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-133">Item elements (*.cs* files) that are inputs to  Razor compilation targets.</span></span> <span data-ttu-id="c70a9-134">이 ItemGroup을 사용하여 Razor 어셈블리에 컴파일할 추가 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-134">Use this ItemGroup to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="c70a9-135">코드에 사용된 항목 요소는 Razor 어셈블리의 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-135">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="c70a9-136">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-136">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="c70a9-137">항목 요소에서 생성 된 Razor 어셈블리에 포함 리소스로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-137">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="c70a9-138">속성</span><span class="sxs-lookup"><span data-stu-id="c70a9-138">Property</span></span> | <span data-ttu-id="c70a9-139">설명</span><span class="sxs-lookup"><span data-stu-id="c70a9-139">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="c70a9-140">Razor에서 생성한 어셈블리의 파일 이름(확장명 제외).</span><span class="sxs-lookup"><span data-stu-id="c70a9-140">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="c70a9-141">Razor 출력 디렉터리.</span><span class="sxs-lookup"><span data-stu-id="c70a9-141">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="c70a9-142">Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-142">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="c70a9-143">유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-143">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="c70a9-144">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="c70a9-144">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="c70a9-145">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-145">Default is `true`.</span></span> <span data-ttu-id="c70a9-146">때 `true`를 포함 *web.config*, *.json*, 및 *.cshtml* 프로젝트의 내용으로는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-146">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="c70a9-147">통해 참조 될 때 `Microsoft.NET.Sdk.Web`, 아래에 있는 파일 *wwwroot* 및 구성 파일도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-147">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="c70a9-148">`true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-148">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="c70a9-149">때 `true`, 생성을 *.cs* 로 지정 된 특성을 포함 하는 파일 `RazorAssemblyAttribute` 컴파일 출력에 파일을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-149">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="c70a9-150">`true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-150">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="c70a9-151">때 `true`, 복사본 `RazorGenerate` 항목 (*.cshtml*) 파일을 게시 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-151">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="c70a9-152">일반적으로 Razor 파일 컴파일 빌드 시간 또는 게시 시간에에서 참여 하는 경우 게시 된 앱에 대 한 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-152">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="c70a9-153">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-153">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="c70a9-154">`true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-154">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="c70a9-155">일반적으로 참조 어셈블리 Razor 컴파일 빌드 시간 또는 게시 시간에 발생 하는 경우 게시 된 앱에 대 한 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-155">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="c70a9-156">로 `true` 게시 된 앱에 런타임 컴파일이 필요 하면 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-156">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="c70a9-157">예를 들어, 값을 설정 `true` 앱을 수정 하는 경우 *.cshtml* 런타임에 파일 또는 포함 된 뷰를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-157">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="c70a9-158">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-158">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="c70a9-159">때 `true`, 모든 Razor 콘텐츠 항목 (*.cshtml* 파일) 생성된 된 NuGet 패키지에 포함 되도록 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-159">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="c70a9-160">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-160">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="c70a9-161">`true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate(*.cshtml*) 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-161">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="c70a9-162">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-162">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="c70a9-163">`true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-163">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="c70a9-164">기본값은 `UseSharedCompilation`입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-164">Defaults to the value of `UseSharedCompilation`.</span></span> |

<span data-ttu-id="c70a9-165">속성에 대한 자세한 내용은 [MSBuild 속성](/visualstudio/msbuild/msbuild-properties)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c70a9-165">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="c70a9-166">대상</span><span class="sxs-lookup"><span data-stu-id="c70a9-166">Targets</span></span>

<span data-ttu-id="c70a9-167">Razor SDK는 두 기본 대상을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-167">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="c70a9-168">`RazorGenerate` &ndash; 코드 생성 *.cs* 에서 파일을 `RazorGenerate` 항목 요소가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-168">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="c70a9-169">이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorGenerateDependsOn` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-169">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="c70a9-170">`RazorCompile` &ndash; 생성 된 컴파일합니다 *.cs* Razor 어셈블리 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-170">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="c70a9-171">이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorCompileDependsOn`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-171">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="c70a9-172">Razor 뷰의 런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="c70a9-172">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="c70a9-173">기본적으로 Razor SDK는 런타임 컴파일을 수행하는 데 필요한 참조 어셈블리를 게시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-173">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="c70a9-174">이로 인해 애플리케이션 모델이 런타임 컴파일을 사용하는 경우 컴파일 오류가 발생합니다&mdash;(예: 앱이 게시된 후에 앱에서는 포함된 보기 또는 변경 내용 보기를 사용함).</span><span class="sxs-lookup"><span data-stu-id="c70a9-174">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="c70a9-175">`CopyRefAssembliesToPublishDirectory`를 `true`로 설정하여 계속 참조 어셈블리를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="c70a9-175">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="c70a9-176">웹 앱에 대 한 앱을 대상으로 확인 된 `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="c70a9-176">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>
