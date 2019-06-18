---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor 페이지를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: fa69e4840377e0c1c8291c7ba9305a27bd3e6b82
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196370"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="db6c3-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="db6c3-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="db6c3-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="db6c3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="db6c3-105">개요</span><span class="sxs-lookup"><span data-stu-id="db6c3-105">Overview</span></span>

<span data-ttu-id="db6c3-106">합니다 [!INCLUDE[](~/includes/2.1-SDK.md)] 포함 된 `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span><span class="sxs-lookup"><span data-stu-id="db6c3-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="db6c3-107">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="db6c3-107">The Razor SDK:</span></span>

* <span data-ttu-id="db6c3-108">ASP.NET Core MVC 기반 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트의 빌드, 패키징 및 게시와 관련된 환경을 표준화합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="db6c3-109">Razor 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 집합이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="db6c3-110">Razor SDK에 포함 되어는 `<Content>` 요소는 `Include` 특성이로 설정 합니다 `**\*.cshtml` 와일드 카드 사용 패턴.</span><span class="sxs-lookup"><span data-stu-id="db6c3-110">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="db6c3-111">일치 하는 파일 게시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="db6c3-112">Razor SDK에 포함 `<Content>` 요소 `Include` 특성 설정 합니다 `**\*.cshtml` 및 `**\*.razor` 와일드 카드 사용 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-112">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="db6c3-113">일치 하는 파일 게시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-113">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="db6c3-114">전제 조건</span><span class="sxs-lookup"><span data-stu-id="db6c3-114">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="db6c3-115">Razor SDK 사용</span><span class="sxs-lookup"><span data-stu-id="db6c3-115">Use the Razor SDK</span></span>

<span data-ttu-id="db6c3-116">대부분의 웹 앱을 명시적으로 Razor SDK를 참조할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-116">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

<span data-ttu-id="db6c3-117">Razor SDK를 사용하여 Razor 보기 또는 Razor 페이지를 포함하는 클래스 라이브러리를 빌드하려면:</span><span class="sxs-lookup"><span data-stu-id="db6c3-117">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="db6c3-118">`Microsoft.NET.Sdk` 대신 `Microsoft.NET.Sdk.Razor`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-118">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="db6c3-119">일반적으로 패키지에 대 한 참조 `Microsoft.AspNetCore.Mvc` Razor 페이지 및 Razor 뷰 컴파일 및 빌드하는 데 필요한 추가 종속성을 수신 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-119">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="db6c3-120">최소한 프로젝트에 대 한 패키지 참조를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-120">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="db6c3-121">`Microsoft.AspNetCore.Razor.Design` 패키지를 프로젝트에 대 한 Razor 컴파일 작업 및 대상에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-121">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="db6c3-122">이전 패키지는 `Microsoft.AspNetCore.Mvc`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-122">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="db6c3-123">다음 태그는 ASP.NET Core Razor 페이지 앱에 대 한 Razor 파일 작성 하려면 Razor SDK를 사용 하는 프로젝트 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-123">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="db6c3-124">`Microsoft.AspNetCore.Razor.Design` 하 고 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 패키지에 포함 된 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-124">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="db6c3-125">그러나 버전-작음 `Microsoft.AspNetCore.App` 패키지 참조는 메타 패키지의 최신 버전을 포함 하지 않는 앱에 제공 `Microsoft.AspNetCore.Razor.Design`합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-125">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="db6c3-126">프로젝트의 일관 된 버전을 참조 해야 합니다 `Microsoft.AspNetCore.Razor.Design` (또는 `Microsoft.AspNetCore.Mvc`) Razor에 대 한 최신 빌드 시간 수정 프로그램이 포함 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-126">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="db6c3-127">자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/Razor/issues/2553)합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-127">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="db6c3-128">속성</span><span class="sxs-lookup"><span data-stu-id="db6c3-128">Properties</span></span>

<span data-ttu-id="db6c3-129">다음 속성은 Razor의 SDK 동작을 프로젝트 빌드의 일부로 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-129">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="db6c3-130">`RazorCompileOnBuild` &ndash; 때 `true`, 컴파일하고 프로젝트 빌드의 일부로 Razor 어셈블리를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-130">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="db6c3-131">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-131">Defaults to `true`.</span></span>
* <span data-ttu-id="db6c3-132">`RazorCompileOnPublish` &ndash; 때 `true`, 컴파일 및 Razor 어셈블리 프로젝트를 게시 하는 일환으로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-132">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="db6c3-133">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-133">Defaults to `true`.</span></span>

<span data-ttu-id="db6c3-134">속성 및 다음 표에 항목을 입력 및 출력 Razor SDK를 구성 하려면 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-134">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

| <span data-ttu-id="db6c3-135">항목</span><span class="sxs-lookup"><span data-stu-id="db6c3-135">Items</span></span> | <span data-ttu-id="db6c3-136">설명</span><span class="sxs-lookup"><span data-stu-id="db6c3-136">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="db6c3-137">코드 생성 대상에 입력되는 항목 요소( *.cshtml* 파일).</span><span class="sxs-lookup"><span data-stu-id="db6c3-137">Item elements (*.cshtml* files) that are inputs to code generation targets.</span></span> |
| `RazorCompile` | <span data-ttu-id="db6c3-138">항목 요소 ( *.cs* 파일)는 Razor 컴파일 대상에 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-138">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="db6c3-139">이 사용 하 여 `ItemGroup` Razor 어셈블리로 컴파일할 수 추가 파일을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-139">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="db6c3-140">코드에 사용된 항목 요소는 Razor 어셈블리의 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-140">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="db6c3-141">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-141">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="db6c3-142">항목 요소에서 생성 된 Razor 어셈블리에 포함 리소스로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-142">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="db6c3-143">속성</span><span class="sxs-lookup"><span data-stu-id="db6c3-143">Property</span></span> | <span data-ttu-id="db6c3-144">설명</span><span class="sxs-lookup"><span data-stu-id="db6c3-144">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="db6c3-145">Razor에서 생성한 어셈블리의 파일 이름(확장명 제외).</span><span class="sxs-lookup"><span data-stu-id="db6c3-145">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="db6c3-146">Razor 출력 디렉터리.</span><span class="sxs-lookup"><span data-stu-id="db6c3-146">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="db6c3-147">Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-147">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="db6c3-148">유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-148">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="db6c3-149">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="db6c3-149">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="db6c3-150">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-150">Default is `true`.</span></span> <span data-ttu-id="db6c3-151">때 `true`를 포함 *web.config*, *.json*, 및 *.cshtml* 프로젝트의 내용으로는 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-151">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="db6c3-152">통해 참조 될 때 `Microsoft.NET.Sdk.Web`, 아래에 있는 파일 *wwwroot* 및 구성 파일도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-152">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="db6c3-153">`true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-153">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="db6c3-154">때 `true`, 생성을 *.cs* 로 지정 된 특성을 포함 하는 파일 `RazorAssemblyAttribute` 컴파일 출력에 파일을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-154">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="db6c3-155">`true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-155">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="db6c3-156">때 `true`, 복사본 `RazorGenerate` 항목 ( *.cshtml*) 파일을 게시 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-156">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="db6c3-157">일반적으로 Razor 파일 컴파일 빌드 시간 또는 게시 시간에에서 참여 하는 경우 게시 된 앱에 대 한 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-157">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="db6c3-158">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-158">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="db6c3-159">`true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-159">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="db6c3-160">일반적으로 참조 어셈블리 Razor 컴파일 빌드 시간 또는 게시 시간에 발생 하는 경우 게시 된 앱에 대 한 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-160">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="db6c3-161">로 `true` 게시 된 앱에 런타임 컴파일이 필요 하면 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-161">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="db6c3-162">예를 들어, 값을 설정 `true` 앱을 수정 하는 경우 *.cshtml* 런타임에 파일 또는 포함 된 뷰를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-162">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="db6c3-163">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-163">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="db6c3-164">때 `true`, 모든 Razor 콘텐츠 항목 ( *.cshtml* 파일) 생성된 된 NuGet 패키지에 포함 되도록 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-164">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="db6c3-165">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-165">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="db6c3-166">`true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate( *.cshtml*) 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-166">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="db6c3-167">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-167">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="db6c3-168">`true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-168">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="db6c3-169">기본값은 `UseSharedCompilation`입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-169">Defaults to the value of `UseSharedCompilation`.</span></span> |

<span data-ttu-id="db6c3-170">속성에 대한 자세한 내용은 [MSBuild 속성](/visualstudio/msbuild/msbuild-properties)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db6c3-170">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="db6c3-171">대상</span><span class="sxs-lookup"><span data-stu-id="db6c3-171">Targets</span></span>

<span data-ttu-id="db6c3-172">Razor SDK는 두 기본 대상을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-172">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="db6c3-173">`RazorGenerate` &ndash; 코드 생성 *.cs* 에서 파일을 `RazorGenerate` 항목 요소가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-173">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="db6c3-174">이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorGenerateDependsOn` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-174">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="db6c3-175">`RazorCompile` &ndash; 생성 된 컴파일합니다 *.cs* Razor 어셈블리 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-175">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="db6c3-176">이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorCompileDependsOn`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-176">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="db6c3-177">Razor 뷰의 런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="db6c3-177">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="db6c3-178">기본적으로 Razor SDK는 런타임 컴파일을 수행하는 데 필요한 참조 어셈블리를 게시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-178">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="db6c3-179">이로 인해 애플리케이션 모델이 런타임 컴파일을 사용하는 경우 컴파일 오류가 발생합니다&mdash;(예: 앱이 게시된 후에 앱에서는 포함된 보기 또는 변경 내용 보기를 사용함).</span><span class="sxs-lookup"><span data-stu-id="db6c3-179">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="db6c3-180">`CopyRefAssembliesToPublishDirectory`를 `true`로 설정하여 계속 참조 어셈블리를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-180">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="db6c3-181">웹 앱에 대 한 앱을 대상으로 확인 된 `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="db6c3-181">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="db6c3-182">Razor 언어 버전</span><span class="sxs-lookup"><span data-stu-id="db6c3-182">Razor language version</span></span>

<span data-ttu-id="db6c3-183">대상으로 할 때를 `Microsoft.NET.Sdk.Web` SDK Razor 언어 버전에서 유추 되는 앱의 대상 프레임 워크 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="db6c3-183">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="db6c3-184">프로젝트의 대상으로 하는 `Microsoft.NET.Sdk.Razor` SDK 또는 유추 된 값 보다 다른 Razor 언어 버전을 앱에 필요한는 드문 경우 버전을 설정 하 여 구성할 수 있습니다는 `<RazorLangVersion>` 앱의 프로젝트 파일에서 속성:</span><span class="sxs-lookup"><span data-stu-id="db6c3-184">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="db6c3-185">추가 자료</span><span class="sxs-lookup"><span data-stu-id="db6c3-185">Additional resources</span></span>

* [<span data-ttu-id="db6c3-186">.NET Core용 csproj 형식에 대한 추가 사항</span><span class="sxs-lookup"><span data-stu-id="db6c3-186">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="db6c3-187">일반적인 MSBuild 프로젝트 항목</span><span class="sxs-lookup"><span data-stu-id="db6c3-187">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
