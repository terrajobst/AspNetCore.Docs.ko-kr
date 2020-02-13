---
title: ASP.NET Core를 사용하여 클래스 라이브러리에서 재사용 가능한 Razor UI
author: Rick-Anderson
description: 부분 뷰를 사용 하 여 ASP.NET Core에서 클래스 라이브러리에 다시 사용할 수 있는 Razor UI를 만드는 방법에 설명 합니다.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 8813244ea6d00b170d9f95d12743e9fee38bf810
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172655"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="5e3cd-103">ASP.NET Core에서 Razor 클래스 라이브러리 프로젝트를 사용 하 여 다시 사용할 수 있는 UI 만들기</span><span class="sxs-lookup"><span data-stu-id="5e3cd-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="5e3cd-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5e3cd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e3cd-105">Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [razor 구성 요소](xref:blazor/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components)및 데이터 모델을 rcl (razor 클래스 라이브러리)로 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="5e3cd-106">RCL은 패키지되고 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="5e3cd-107">애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="5e3cd-108">보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="5e3cd-109">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5e3cd-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="5e3cd-110">Razor UI를 포함하는 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="5e3cd-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e3cd-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e3cd-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e3cd-112">Visual Studio에서 새 **프로젝트 만들기를**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="5e3cd-113">**Razor 클래스 라이브러리** > **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="5e3cd-114">라이브러리 이름 (예: "RazorClassLib")을 > 하 여 **만듭니다**.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="5e3cd-115">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="5e3cd-116">보기를 지원 해야 하는 경우 **지원 페이지 및 보기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="5e3cd-117">기본적으로 Razor Pages만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="5e3cd-118">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-118">Select **Create**.</span></span>

<span data-ttu-id="5e3cd-119">RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="5e3cd-120">**지원 페이지 및 보기** 옵션은 페이지 및 보기를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5e3cd-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5e3cd-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5e3cd-122">명령줄에서 `dotnet new razorclasslib`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="5e3cd-123">예들 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="5e3cd-124">RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="5e3cd-125">`--support-pages-and-views` 옵션 (`dotnet new razorclasslib --support-pages-and-views`)을 전달 하 여 페이지 및 보기에 대 한 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="5e3cd-126">자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="5e3cd-127">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="5e3cd-128">RCL에 Razor 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="5e3cd-129">ASP.NET Core 템플릿은 RCL 콘텐츠가 *영역* 폴더에 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="5e3cd-130">`~/Areas/Pages`대신 `~/Pages`에서 콘텐츠를 노출 하는 RCL을 만들려면 [Rcl 페이지 레이아웃](#rcl-pages-layout) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="5e3cd-131">RCL 콘텐츠 참조</span><span class="sxs-lookup"><span data-stu-id="5e3cd-131">Reference RCL content</span></span>

<span data-ttu-id="5e3cd-132">RCL은 다음에서 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="5e3cd-133">NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-133">NuGet package.</span></span> <span data-ttu-id="5e3cd-134">[NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="5e3cd-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="5e3cd-136">[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="5e3cd-137">보기, 부분 보기 및 페이지 재정의</span><span class="sxs-lookup"><span data-stu-id="5e3cd-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="5e3cd-138">보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="5e3cd-139">예를 들어 *WebApp1/Areas/MyFeature/Pages/* WebApp1에 추가 하 고 WebApp1의는 rcl의 페이지 1 보다 우선 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="5e3cd-140">샘플 다운로드에서 *WebApp1/Areas/MyFeature2*를 *WebApp1/Areas/MyFeature*로 이름을 바꾸어 우선적으로 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="5e3cd-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 보기를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="5e3cd-142">새 위치를 나타내기 위해 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="5e3cd-143">해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="5e3cd-144">RCL 페이지 레이아웃</span><span class="sxs-lookup"><span data-stu-id="5e3cd-144">RCL Pages layout</span></span>

<span data-ttu-id="5e3cd-145">RCL 콘텐츠를 웹 앱의 *Pages* 폴더에 포함 된 것 처럼 참조 하려면 다음 파일 구조를 사용 하 여 rcl 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="5e3cd-146">*RazorUIClassLib/페이지*</span><span class="sxs-lookup"><span data-stu-id="5e3cd-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="5e3cd-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="5e3cd-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="5e3cd-148">*RazorUIClassLib/Pages/Shared* 에 두 개의 부분 파일이 포함 되어 있다고 가정 합니다. *_Header. cshtml* 및 *_Footer*.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="5e3cd-149">`<partial>` 태그를 *_Layout. cshtml* 파일에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="5e3cd-150">정적 자산을 사용 하 여 RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="5e3cd-150">Create an RCL with static assets</span></span>

<span data-ttu-id="5e3cd-151">Rcl에는 rcl 또는 RCL의 소비 앱에서 참조할 수 있는 도우미 정적 자산이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="5e3cd-152">ASP.NET Core를 사용 하면 소비 하는 앱에서 사용할 수 있는 정적 자산이 포함 된 RCLs를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="5e3cd-153">RCL의 일부로 동반 자산을 포함 하려면 클래스 라이브러리에 *wwwroot* 폴더를 만들고 해당 폴더에 필요한 파일을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="5e3cd-154">RCL을 압축 하면 *wwwroot* 폴더의 모든 관련 자산이 패키지에 자동으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="5e3cd-155">정적 자산 제외</span><span class="sxs-lookup"><span data-stu-id="5e3cd-155">Exclude static assets</span></span>

<span data-ttu-id="5e3cd-156">정적 자산을 제외 하려면 프로젝트 파일의 `$(DefaultItemExcludes)` 속성 그룹에 원하는 제외 경로를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="5e3cd-157">항목을 세미콜론 (`;`)으로 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="5e3cd-158">다음 예제에서 *wwwroot* 폴더의 *lib .css* 스타일 시트는 정적 자산으로 간주 되지 않으며 게시 된 rcl에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="5e3cd-159">Typescript 통합</span><span class="sxs-lookup"><span data-stu-id="5e3cd-159">Typescript integration</span></span>

<span data-ttu-id="5e3cd-160">RCL에 TypeScript 파일을 포함 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="5e3cd-161">TypeScript *폴더 외부에 TypeScript* 파일 (.ts *)을 저장 합니다.*</span><span class="sxs-lookup"><span data-stu-id="5e3cd-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="5e3cd-162">예를 들어 *클라이언트* 폴더에 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="5e3cd-163">*Wwwroot* 폴더에 대 한 TypeScript 빌드 출력을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="5e3cd-164">프로젝트 파일에서 `PropertyGroup` 내부에 `TypescriptOutDir` 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="5e3cd-165">프로젝트 파일의 `PropertyGroup` 내부에 다음 대상을 추가 하 여 TypeScript 대상을 `ResolveCurrentProjectStaticWebAssets` 대상의 종속성으로 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="5e3cd-166">참조 된 RCL에서 콘텐츠 사용</span><span class="sxs-lookup"><span data-stu-id="5e3cd-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="5e3cd-167">RCL의 *wwwroot* 폴더에 포함 된 파일은 `_content/{LIBRARY NAME}/`접두사의 rcl 또는 소비 앱에 노출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="5e3cd-168">예를 들어, *Razor.* t c m. c a t. c a t. c a t. `_content/Razor.Class.Lib/`c a t.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="5e3cd-169">NuGet 패키지를 생성할 때 어셈블리 이름이 패키지 ID와 다른 경우 `{LIBRARY NAME}`의 패키지 ID를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="5e3cd-170">소비 앱은 `<script>`, `<style>`, `<img>`및 기타 HTML 태그를 사용 하 여 라이브러리에서 제공 하는 정적 자산을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="5e3cd-171">소비 하는 앱은 `Startup.Configure`에서 [정적 파일 지원을](xref:fundamentals/static-files) 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="5e3cd-172">빌드 출력 (`dotnet run`)에서 사용 하는 앱을 실행 하는 경우 개발 환경에서 정적 웹 자산이 기본적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="5e3cd-173">빌드 출력에서 실행 될 때 다른 환경에서 자산을 지원 하려면 *Program.cs*의 호스트 작성기에서 `UseStaticWebAssets`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="5e3cd-174">게시 된 출력 (`dotnet publish`)에서 앱을 실행 하는 경우 `UseStaticWebAssets`를 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="5e3cd-175">다중 프로젝트 개발 흐름</span><span class="sxs-lookup"><span data-stu-id="5e3cd-175">Multi-project development flow</span></span>

<span data-ttu-id="5e3cd-176">소비 앱이 실행 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="5e3cd-176">When the consuming app runs:</span></span>

* <span data-ttu-id="5e3cd-177">RCL의 자산은 원래 폴더에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="5e3cd-178">자산이 소비 앱으로 이동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="5e3cd-179">Rcl의 *wwwroot* 폴더 내에서 변경 되는 내용은 rcl이 다시 작성 된 후 소비 앱에 반영 되며 소비 하는 앱을 다시 빌드하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="5e3cd-180">RCL을 빌드할 때 정적 웹 자산 위치를 설명 하는 매니페스트가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="5e3cd-181">소비 앱은 런타임에 매니페스트를 읽어 참조 된 프로젝트 및 패키지의 자산을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="5e3cd-182">새 자산이 RCL에 추가 되 면 사용 하는 앱이 새 자산에 액세스할 수 있으려면 먼저 RCL을 다시 작성 하 여 매니페스트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="5e3cd-183">게시</span><span class="sxs-lookup"><span data-stu-id="5e3cd-183">Publish</span></span>

<span data-ttu-id="5e3cd-184">앱이 게시 되 면 모든 참조 된 프로젝트 및 패키지의 동반 자산이 `_content/{LIBRARY NAME}/`아래 게시 된 앱의 *wwwroot* 폴더로 복사 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5e3cd-185">Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [razor 구성 요소](xref:blazor/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components)및 데이터 모델을 rcl (razor 클래스 라이브러리)로 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="5e3cd-186">RCL은 패키지되고 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="5e3cd-187">애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="5e3cd-188">보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="5e3cd-189">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5e3cd-189">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="5e3cd-190">Razor UI를 포함하는 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="5e3cd-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e3cd-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e3cd-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5e3cd-192">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5e3cd-193">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5e3cd-194">라이브러리 이름 지정(예: "RazorClassLib") > **확인**입니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="5e3cd-195">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="5e3cd-196">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="5e3cd-197">**Razor 클래스 라이브러리** > **확인을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="5e3cd-198">RCL에는 다음과 같은 프로젝트 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5e3cd-199">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5e3cd-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5e3cd-200">명령줄에서 `dotnet new razorclasslib`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="5e3cd-201">예들 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="5e3cd-202">자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="5e3cd-203">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="5e3cd-204">RCL에 Razor 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="5e3cd-205">ASP.NET Core 템플릿은 RCL 콘텐츠가 *영역* 폴더에 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="5e3cd-206">`~/Areas/Pages`대신 `~/Pages`에서 콘텐츠를 노출 하는 RCL을 만들려면 [Rcl 페이지 레이아웃](#rcl-pages-layout) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="5e3cd-207">RCL 콘텐츠 참조</span><span class="sxs-lookup"><span data-stu-id="5e3cd-207">Reference RCL content</span></span>

<span data-ttu-id="5e3cd-208">RCL은 다음에서 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="5e3cd-209">NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-209">NuGet package.</span></span> <span data-ttu-id="5e3cd-210">[NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="5e3cd-211">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="5e3cd-212">[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="5e3cd-213">연습: RCL 프로젝트 만들기 및 Razor Pages 프로젝트에서 사용</span><span class="sxs-lookup"><span data-stu-id="5e3cd-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="5e3cd-214">[전체 프로젝트](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)를 다운로드하여 만들지 않고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-214">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="5e3cd-215">샘플 다운로드에는 프로젝트를 쉽게 테스트하게 하는 링크와 추가 코드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="5e3cd-216">샘플 다운로드 대 단계별 지침에 대한 주석을 사용하여 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/6098)에서 사용자 의견을 그대로 둘 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-216">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="5e3cd-217">다운로드 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="5e3cd-217">Test the download app</span></span>

<span data-ttu-id="5e3cd-218">완료된 앱을 다운로드하지 않고 연습 프로젝트를 만들려는 경우 [다음 섹션](#create-an-rcl)으로 건너 뜁니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e3cd-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e3cd-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e3cd-220">Visual Studio에서 *.sln* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="5e3cd-221">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-221">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5e3cd-222">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5e3cd-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5e3cd-223">*cli* 디렉터리의 명령 프롬프트에서 RCL 및 웹앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="5e3cd-224">*WebApp1* 디렉터리로 이동해 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="5e3cd-225">[WebApp1 테스트](#test-webapp1)의 지침 준수</span><span class="sxs-lookup"><span data-stu-id="5e3cd-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="5e3cd-226">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="5e3cd-226">Create an RCL</span></span>

<span data-ttu-id="5e3cd-227">이 섹션에서는 RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-227">In this section, an RCL is created.</span></span> <span data-ttu-id="5e3cd-228">Razor 파일이 RCL에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e3cd-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e3cd-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e3cd-230">RCL 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-230">Create the RCL project:</span></span>

* <span data-ttu-id="5e3cd-231">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5e3cd-232">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5e3cd-233">앱 이름을 **RazorUIClassLib** > **확인**합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="5e3cd-234">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="5e3cd-235">**Razor 클래스 라이브러리** > **확인을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="5e3cd-236">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*이라는 Razor 부분 보기 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5e3cd-237">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5e3cd-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5e3cd-238">명령줄에서 다음을 실행하세요.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="5e3cd-239">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="5e3cd-239">The preceding commands:</span></span>

* <span data-ttu-id="5e3cd-240">`RazorUIClassLib` RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="5e3cd-241">Razor _Message 페이지를 만들어 RCL에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="5e3cd-242">`-np` 매개 변수는 `PageModel`가 없는 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="5e3cd-243">[_ViewStart cshtml](xref:mvc/views/layout#running-code-before-each-view) 파일을 만들어 rcl에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="5e3cd-244">*_ViewStart* 파일은 Razor Pages 프로젝트의 레이아웃 (다음 섹션에 추가 됨)을 사용 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="5e3cd-245">Razor 파일 및 폴더를 프로젝트에 추가</span><span class="sxs-lookup"><span data-stu-id="5e3cd-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="5e3cd-246">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*에서 태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="5e3cd-247">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml*에서 태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="5e3cd-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`은 부분 보기(`<partial name="_Message" />`)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="5e3cd-249">`@addTagHelper` 지시문을 포함하지 않고 *_ViewImports.cshtml* 파일을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5e3cd-250">예들 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="5e3cd-251">_ViewImports에 대 한 자세한 내용은 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives) 를 참조 하세요 *.*</span><span class="sxs-lookup"><span data-stu-id="5e3cd-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="5e3cd-252">컴파일러 오류가 없는지 확인하려면 클래스 라이브러리를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="5e3cd-253">빌드 출력은 *RazorUIClassLib.dll* 및 *RazorUIClassLib.Views.dll*을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="5e3cd-254">*RazorUIClassLib.Views.dll*은 컴파일된 Razor 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="5e3cd-255">Razor 페이지 프로젝트에서 Razor UI 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="5e3cd-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e3cd-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e3cd-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e3cd-257">Razor 페이지 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="5e3cd-258">**솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭 하 > >**새 프로젝트**를 **추가** 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="5e3cd-259">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="5e3cd-260">**WebApp1** 앱 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="5e3cd-261">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="5e3cd-262">**웹 응용 프로그램** > **확인을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="5e3cd-263">**솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **스타트업 프로젝트로 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="5e3cd-264">**솔루션 탐색기**에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭 하 고 **빌드 종속성** > **프로젝트 종속성**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="5e3cd-265">**RazorUIClassLib**를 **WebApp1**의 종속성으로 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="5e3cd-266">**솔루션 탐색기**에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭 하 고 > **참조** **추가** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="5e3cd-267">**참조 관리자** 대화 상자에서 **RazorUIClassLib** > **OK**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="5e3cd-268">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-268">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5e3cd-269">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5e3cd-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5e3cd-270">Razor Pages 앱 및 RCL을 포함 하는 Razor Pages 웹 앱 및 솔루션 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="5e3cd-271">웹앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="5e3cd-272">WebApp1 테스트</span><span class="sxs-lookup"><span data-stu-id="5e3cd-272">Test WebApp1</span></span>

<span data-ttu-id="5e3cd-273">`/MyFeature/Page1`로 이동 하 여 Razor UI 클래스 라이브러리가 사용 중인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="5e3cd-274">보기, 부분 보기 및 페이지 재정의</span><span class="sxs-lookup"><span data-stu-id="5e3cd-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="5e3cd-275">보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="5e3cd-276">예를 들어 *WebApp1/Areas/MyFeature/Pages/* WebApp1에 추가 하 고 WebApp1의는 rcl의 페이지 1 보다 우선 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="5e3cd-277">샘플 다운로드에서 *WebApp1/Areas/MyFeature2*를 *WebApp1/Areas/MyFeature*로 이름을 바꾸어 우선적으로 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="5e3cd-278">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 보기를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="5e3cd-279">새 위치를 나타내기 위해 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="5e3cd-280">해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="5e3cd-281">RCL 페이지 레이아웃</span><span class="sxs-lookup"><span data-stu-id="5e3cd-281">RCL Pages layout</span></span>

<span data-ttu-id="5e3cd-282">RCL 콘텐츠를 웹 앱의 *Pages* 폴더에 포함 된 것 처럼 참조 하려면 다음 파일 구조를 사용 하 여 rcl 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="5e3cd-283">*RazorUIClassLib/페이지*</span><span class="sxs-lookup"><span data-stu-id="5e3cd-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="5e3cd-284">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="5e3cd-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="5e3cd-285">*RazorUIClassLib/Pages/Shared* 에 두 개의 부분 파일이 포함 되어 있다고 가정 합니다. *_Header. cshtml* 및 *_Footer*.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="5e3cd-286">`<partial>` 태그를 *_Layout. cshtml* 파일에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e3cd-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5e3cd-287">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5e3cd-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
