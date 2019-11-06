---
title: ASP.NET Core의 Razor 파일 컴파일
author: rick-anderson
description: Razor 파일의 컴파일이 ASP.NET Core 앱에서 발생하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/20/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 0aa632bce32ef44f65d92639284c64c1d00e952e
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080818"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="5e28f-103">ASP.NET Core의 Razor 파일 컴파일</span><span class="sxs-lookup"><span data-stu-id="5e28f-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="5e28f-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5e28f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="5e28f-105">Razor 파일은 런타임 시에 관련 MVC 보기가 호출될 때 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="5e28f-106">빌드 시 Razor 파일 게시는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="5e28f-107">Razor 파일은 필요에 따라 사전 컴파일 도구를 사용하여 게시 시에 컴파일되어 앱과 함께 배포될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5e28f-108">Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="5e28f-109">빌드 시 Razor 파일 게시는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="5e28f-110">Razor 파일은 필요에 따라 사전 컴파일 도구를 사용하여 게시 시에 컴파일되어 앱과 함께 배포될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="5e28f-111">Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="5e28f-112">Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e28f-113">Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-113">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="5e28f-114">응용 프로그램을 구성하여 런타임 컴파일을 선택적으로 활성시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-114">Runtime compilation may be optionally enabled by configuring your application.</span></span>

::: moniker-end

## <a name="razor-compilation"></a><span data-ttu-id="5e28f-115">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="5e28f-115">Razor compilation</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="5e28f-116">Razor 파일의 빌드 및 게시 시 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-116">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="5e28f-117">런타임 컴파일이 활성화되면 Razor 파일이 편집될 경우 업데이트될 수 있도록 빌드 타임 컴파일을 보완합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-117">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="5e28f-118">Razor 파일의 빌드 및 게시 시 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-118">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="5e28f-119">Razor 파일이 수정된 후에 편집하는 작업은 빌드 시에 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-119">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="5e28f-120">기본적으로 *.cshtml* 파일을 제외한 컴파일된 *Views.dll* 또는 Razor 파일을 컴파일하는 데 필요한 참조 어셈블리만 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-120">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5e28f-121">사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-121">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5e28f-122">[Razor SDK](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-122">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="5e28f-123">Razor SDK는 미리 컴파일 특정 속성이 프로젝트 파일에서 설정되지 않은 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-123">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="5e28f-124">예를 들어 *.csproj* 파일의 `MvcRazorCompileOnPublish` 속성을 `true`로 설정하면 Razor SDK가 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-124">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5e28f-125">프로젝트가 .NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 패키지를 설치하세요.</span><span class="sxs-lookup"><span data-stu-id="5e28f-125">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="5e28f-126">프로젝트가 .NET Core를 대상으로 하는 경우 변경 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-126">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="5e28f-127">ASP.NET Core 2.x 프로젝트 템플릿은 기본적으로 `MvcRazorCompileOnPublish` 속성을 암시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-127">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="5e28f-128">다라서 이 요소는 *.csproj* 파일에서 안전하게 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-128">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5e28f-129">사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-129">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5e28f-130">[Razor SDK](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-130">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="5e28f-131">ASP.NET Core 2.0에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)를 수행하는 경우 Razor 파일 미리 컴파일은 사용 불가능합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-131">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="5e28f-132">`MvcRazorCompileOnPublish` 속성을 `true`로 설정하고 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 패키지를 설치하세요.</span><span class="sxs-lookup"><span data-stu-id="5e28f-132">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="5e28f-133">다음 *.csproj* 샘플은 이러한 설정을 강조 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-133">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="5e28f-134">[.NET Core CLI 게시 명령](/dotnet/core/tools/dotnet-publish)을 사용하여 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)에 대한 앱을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-134">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="5e28f-135">예를 들어 프로젝트 루트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-135">For example, execute the following command at the project root:</span></span>

```dotnetcli
dotnet publish -c Release
```

<span data-ttu-id="5e28f-136">미리 컴파일에 성공하면 컴파일된 Razor 파일이 포함된 *\<project_name>.PrecompiledViews.dll* 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-136">A *\<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="5e28f-137">예를 들어 아래 스크린샷은 *WebApplication1.PrecompiledViews.dll* 내부에서 *Index.cshtml*의 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-137">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![DLL 내에서 Razor 뷰](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a><span data-ttu-id="5e28f-139">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="5e28f-139">Runtime compilation</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="5e28f-140">빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-140">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="5e28f-141">*.cshtml* 파일의 내용이 변경되면 ASP.NET Core MVC는 Razor 파일을 다시 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-141">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5e28f-142">빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-142">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="5e28f-143"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>는 디스크의 파일이 변경되면 Razor 파일(Razor 보기 및 Razor Pages)이 컴파일되고 갱신되는지 여부를 결정하는 값을 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-143">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="5e28f-144">다음의 경우 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-144">The default value is `true` for:</span></span>

* <span data-ttu-id="5e28f-145">앱의 호환성 버전이 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 이하로 설정된 경우</span><span class="sxs-lookup"><span data-stu-id="5e28f-145">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier</span></span>
* <span data-ttu-id="5e28f-146">앱의 호환성 버전이 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 이상으로 설정되고 앱이 개발 환경 <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>에 있는 경우</span><span class="sxs-lookup"><span data-stu-id="5e28f-146">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later and the app is in the Development environment <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span></span> <span data-ttu-id="5e28f-147">즉, Razor 파일은 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>가 명시적으로 설정되지 않는 한 비개발 환경에서 다시 컴파일되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-147">In other words, Razor files would not recompile in non-Development environment unless <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is explicitly set.</span></span>

<span data-ttu-id="5e28f-148">앱의 호환성 버전 설정에 대한 지침과 예는 <xref:mvc/compatibility-version>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e28f-148">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e28f-149">런타임 컴파일은 `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 패키지를 사용하여 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-149">Runtime compilation is enabled using the `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package.</span></span> <span data-ttu-id="5e28f-150">런타임 컴파일을 사용하려면 앱에서</span><span class="sxs-lookup"><span data-stu-id="5e28f-150">To enable runtime compilation, apps must:</span></span>

* <span data-ttu-id="5e28f-151">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e28f-151">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
* <span data-ttu-id="5e28f-152">`AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드 업데이트:</span><span class="sxs-lookup"><span data-stu-id="5e28f-152">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddRazorRuntimeCompilation();
  ```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5e28f-153">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5e28f-153">Additional resources</span></span>

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
