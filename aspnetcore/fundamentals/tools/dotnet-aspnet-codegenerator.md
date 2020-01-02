---
title: dotnet aspnet-codegenerator 명령
author: rick-anderson
description: dotnet aspnet codegenerator 명령은 ASP.NET Core 프로젝트를 스캐폴딩합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081504"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="bff00-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="bff00-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="bff00-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bff00-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bff00-105">`dotnet aspnet-codegenerator` - ASP.NET Core 스캐폴딩 엔진을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="bff00-106">`dotnet aspnet-codegenerator`는 명령줄에서 스캐폴딩하는 데만 필요하며, Visual Studio에서는 스캐폴딩을 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="bff00-107">이 문서는 [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) 이상에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="bff00-108">aspnet-codegenerator 설치</span><span class="sxs-lookup"><span data-stu-id="bff00-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="bff00-109">`dotnet-aspnet-codegenerator`는 설치해야 하는 [전역 도구](/dotnet/core/tools/global-tools)입니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="bff00-110">다음 명령은 `dotnet-aspnet-codegenerator` 도구의 안정적인 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="bff00-111">다음 명령은 `dotnet-aspnet-codegenerator`를 설치된 .NET Core SDK에서 사용 가능한 안정적인 최신 버전으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="bff00-112">개요</span><span class="sxs-lookup"><span data-stu-id="bff00-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="bff00-113">설명</span><span class="sxs-lookup"><span data-stu-id="bff00-113">Description</span></span>

<span data-ttu-id="bff00-114">`dotnet aspnet-codegenerator` 전역 명령은 ASP.NET Core 코드 생성기 및 스캐폴딩 엔진을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="bff00-115">인수</span><span class="sxs-lookup"><span data-stu-id="bff00-115">Arguments</span></span>

`generator`

<span data-ttu-id="bff00-116">실행할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-116">The code generator to run.</span></span> <span data-ttu-id="bff00-117">다음 생성기를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-117">The following generators are available:</span></span>

| <span data-ttu-id="bff00-118">Generator</span><span class="sxs-lookup"><span data-stu-id="bff00-118">Generator</span></span> | <span data-ttu-id="bff00-119">작업</span><span class="sxs-lookup"><span data-stu-id="bff00-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="bff00-120">area</span><span class="sxs-lookup"><span data-stu-id="bff00-120">area</span></span>      | [<span data-ttu-id="bff00-121">영역 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="bff00-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="bff00-122">Controller</span><span class="sxs-lookup"><span data-stu-id="bff00-122">controller</span></span>| [<span data-ttu-id="bff00-123">컨트롤러 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="bff00-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="bff00-124">ID</span><span class="sxs-lookup"><span data-stu-id="bff00-124">identity</span></span>  | [<span data-ttu-id="bff00-125">ID 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="bff00-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="bff00-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="bff00-126">razorpage</span></span> | [<span data-ttu-id="bff00-127">Razor 페이지 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="bff00-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="bff00-128">View</span><span class="sxs-lookup"><span data-stu-id="bff00-128">view</span></span>      | [<span data-ttu-id="bff00-129">보기 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="bff00-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="bff00-130">옵션</span><span class="sxs-lookup"><span data-stu-id="bff00-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="bff00-131">NuGet 패키지 디렉터리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="bff00-132">빌드 구성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-132">Defines the build configuration.</span></span> <span data-ttu-id="bff00-133">기본값은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="bff00-134">사용할 대상 [프레임워크](/dotnet/standard/frameworks)입니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="bff00-135">예: `net46`.</span><span class="sxs-lookup"><span data-stu-id="bff00-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="bff00-136">빌드 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="bff00-137">명령에 대한 간단한 도움말을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="bff00-138">실행하기 전에 프로젝트를 빌드하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-138">Doesn't build the project before running.</span></span> <span data-ttu-id="bff00-139">또한 `--no-restore` 플래그를 암시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="bff00-140">실행할 프로젝트 파일의 경로를 지정합니다(폴더 이름 또는 전체 경로).</span><span class="sxs-lookup"><span data-stu-id="bff00-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="bff00-141">지정하지 않으면 현재 디렉터리로 기본 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="bff00-142">생성기 옵션</span><span class="sxs-lookup"><span data-stu-id="bff00-142">Generator options</span></span>

<span data-ttu-id="bff00-143">다음 섹션에서는 지원되는 생성기에 사용 가능한 옵션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="bff00-144">Area</span><span class="sxs-lookup"><span data-stu-id="bff00-144">Area</span></span>
* <span data-ttu-id="bff00-145">Controller</span><span class="sxs-lookup"><span data-stu-id="bff00-145">Controller</span></span>
* <span data-ttu-id="bff00-146">Identity</span><span class="sxs-lookup"><span data-stu-id="bff00-146">Identity</span></span>  
* <span data-ttu-id="bff00-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="bff00-147">Razorpage</span></span>
* <span data-ttu-id="bff00-148">View</span><span class="sxs-lookup"><span data-stu-id="bff00-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="bff00-149">Area 옵션</span><span class="sxs-lookup"><span data-stu-id="bff00-149">Area options</span></span>

<span data-ttu-id="bff00-150">이 도구는 컨트롤러와 보기가 포함된 ASP.NET Core 웹 프로젝트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="bff00-151">Razor Pages 앱에는 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="bff00-152">사용법: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="bff00-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="bff00-153">앞의 명령은 다음 폴더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="bff00-154">*Areas*</span><span class="sxs-lookup"><span data-stu-id="bff00-154">*Areas*</span></span>
  * <span data-ttu-id="bff00-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="bff00-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="bff00-156">*Controllers*</span><span class="sxs-lookup"><span data-stu-id="bff00-156">*Controllers*</span></span>
    * <span data-ttu-id="bff00-157">*Data*</span><span class="sxs-lookup"><span data-stu-id="bff00-157">*Data*</span></span>
    * <span data-ttu-id="bff00-158">*Models*</span><span class="sxs-lookup"><span data-stu-id="bff00-158">*Models*</span></span>
    * <span data-ttu-id="bff00-159">*Views*</span><span class="sxs-lookup"><span data-stu-id="bff00-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="bff00-160">Controller 옵션</span><span class="sxs-lookup"><span data-stu-id="bff00-160">Controller options</span></span>

<span data-ttu-id="bff00-161">다음 테이블에는 `aspnet-codegenerator` `controller` 및 `razorpage`의 옵션이 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="bff00-162">다음 테이블에는 `aspnet-codegenerator controller`에 고유한 옵션이 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="bff00-163">옵션</span><span class="sxs-lookup"><span data-stu-id="bff00-163">Option</span></span>               | <span data-ttu-id="bff00-164">설명</span><span class="sxs-lookup"><span data-stu-id="bff00-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="bff00-165">--controllerName 또는 -name</span><span class="sxs-lookup"><span data-stu-id="bff00-165">--controllerName or -name</span></span> | <span data-ttu-id="bff00-166">컨트롤러의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-166">Name of the controller.</span></span> |
| <span data-ttu-id="bff00-167">--useAsyncActions 또는 -async</span><span class="sxs-lookup"><span data-stu-id="bff00-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="bff00-168">비동기 컨트롤러 동작을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="bff00-169">--noViews 또는 -nv</span><span class="sxs-lookup"><span data-stu-id="bff00-169">--noViews or -nv</span></span> | <span data-ttu-id="bff00-170">보기를 생성하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="bff00-170">Generate **no** views.</span></span> |
| <span data-ttu-id="bff00-171">-restWithNoViews 또는 -api</span><span class="sxs-lookup"><span data-stu-id="bff00-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="bff00-172">REST 스타일 API를 사용하여 컨트롤러를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="bff00-173">`noViews`로 추정되며 모든 보기 관련된 옵션은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="bff00-174">--readWriteActions 또는 -actions</span><span class="sxs-lookup"><span data-stu-id="bff00-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="bff00-175">모델 없이 읽기/쓰기 동작이 포함된 컨트롤러를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="bff00-176">`-h` 스위치를 사용하여 `aspnet-codegenerator controller` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="bff00-177">`dotnet aspnet-codegenerator controller`의 예제는 [동영상 모델 스캐폴드](/aspnet/core/tutorials/razor-pages/model)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bff00-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="bff00-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="bff00-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="bff00-179">Razor Pages는 새 페이지 이름 및 사용할 템플릿을 지정하여 개별적으로 스캐폴딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="bff00-180">지원되는 템플릿은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="bff00-181">예를 들어 다음 명령은 Edit 템플릿을 사용하여 *MyEdit.cshtml* 및 *MyEdit.cshtml.cs*를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="bff00-182">일반적으로 템플릿 및 생성된 파일 이름은 지정되지 않으며 다음 템플릿이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="bff00-183">다음 테이블에는 `aspnet-codegenerator` `razorpage` 및 `controller`의 옵션이 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="bff00-184">다음 테이블에는 `aspnet-codegenerator razorpage`에 고유한 옵션이 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="bff00-185">옵션</span><span class="sxs-lookup"><span data-stu-id="bff00-185">Option</span></span>               | <span data-ttu-id="bff00-186">설명</span><span class="sxs-lookup"><span data-stu-id="bff00-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="bff00-187">--namespaceName 또는 -namespace</span><span class="sxs-lookup"><span data-stu-id="bff00-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="bff00-188">생성된 PageModel에 사용할 네임스페이스의 이름</span><span class="sxs-lookup"><span data-stu-id="bff00-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="bff00-189">--partialView 또는 -partial</span><span class="sxs-lookup"><span data-stu-id="bff00-189">--partialView or -partial</span></span> | <span data-ttu-id="bff00-190">부분 보기를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-190">Generate a partial view.</span></span> <span data-ttu-id="bff00-191">레이아웃 옵션 -l 및-udl이 지정된 경우 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="bff00-192">--noPageModel 또는 -npm</span><span class="sxs-lookup"><span data-stu-id="bff00-192">--noPageModel or -npm</span></span> | <span data-ttu-id="bff00-193">빈 템플릿에 대 한 PageModel 클래스를 생성하지 않도록 전환</span><span class="sxs-lookup"><span data-stu-id="bff00-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="bff00-194">`-h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bff00-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="bff00-195">`dotnet aspnet-codegenerator razorpage`의 예제는 [동영상 모델 스캐폴드](/aspnet/core/tutorials/razor-pages/model)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bff00-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="bff00-196">Identity</span><span class="sxs-lookup"><span data-stu-id="bff00-196">Identity</span></span>

<span data-ttu-id="bff00-197">[스캐폴드 ID](/aspnet/core/security/authentication/scaffold-identity) 참조</span><span class="sxs-lookup"><span data-stu-id="bff00-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
