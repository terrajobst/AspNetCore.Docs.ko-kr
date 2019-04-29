---
title: ASP.NET Core를 사용하여 클래스 라이브러리에서 재사용 가능한 Razor UI
author: Rick-Anderson
description: 부분 뷰를 사용 하 여 ASP.NET Core에서 클래스 라이브러리에 다시 사용할 수 있는 Razor UI를 만드는 방법에 설명 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/07/2018
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 383b7e49b5dbd0c0f13218aff20abe6421d785b8
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165159"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="3639a-103">ASP.NET Core에서 Razor 클래스 라이브러리 프로젝트를 사용 하 여 다시 사용할 수 있는 UI 만들기</span><span class="sxs-lookup"><span data-stu-id="3639a-103">Create reusable UI using the Razor Class Library project in ASP.NET Core</span></span>

<span data-ttu-id="3639a-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3639a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3639a-105">RCL(Razor 클래스 라이브러리)에 Razor 보기, 페이지, 컨트롤러, 페이지 모델, [보기 구성 요소](xref:mvc/views/view-components) 및 데이터 모델을 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-105">Razor views, pages, controllers, page models, [View components](xref:mvc/views/view-components), and data models can be built into a Razor Class Library (RCL).</span></span> <span data-ttu-id="3639a-106">RCL은 패키지되고 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="3639a-107">애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="3639a-108">보기, 부분 보기 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그(*.cshtml* 파일)가 우선적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="3639a-109">이 기능을 사용 [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="3639a-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="3639a-110">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3639a-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="3639a-111">Razor UI를 포함하는 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="3639a-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3639a-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3639a-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3639a-113">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3639a-114">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3639a-115">라이브러리 이름 지정(예: "RazorClassLib") > **확인**입니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="3639a-116">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="3639a-117">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3639a-118">**Razor 클래스 라이브러리** > **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="3639a-119">Razor 클래스 라이브러리에는 다음 프로젝트 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-119">A Razor Class Library has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3639a-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3639a-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3639a-121">명령줄에서 `dotnet new razorclasslib`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="3639a-122">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="3639a-123">자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="3639a-124">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="3639a-125">RCL에 Razor 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="3639a-126">ASP.NET Core 템플릿은 가정 RCL 콘텐츠를 *영역* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="3639a-127">참조 [RCL 페이지 레이아웃](#afs) 노출 하는 RCL 콘텐츠를 만들려는 `~/Pages` 대신 `~/Areas/Pages`합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-127">See [RCL Pages layout](#afs) to create a RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-razor-class-library-content"></a><span data-ttu-id="3639a-128">Razor 클래스 라이브러리 콘텐츠 참조</span><span class="sxs-lookup"><span data-stu-id="3639a-128">Referencing Razor Class Library content</span></span>

<span data-ttu-id="3639a-129">RCL은 다음에서 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="3639a-130">NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="3639a-130">NuGet package.</span></span> <span data-ttu-id="3639a-131">[NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="3639a-132">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="3639a-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="3639a-133">[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-a-razor-class-library-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="3639a-134">연습: Razor 클래스 라이브러리 프로젝트를 만들고 사용 하 여 Razor 페이지 프로젝트에서</span><span class="sxs-lookup"><span data-stu-id="3639a-134">Walkthrough: Create a Razor Class Library project and use from a Razor Pages project</span></span>

<span data-ttu-id="3639a-135">[전체 프로젝트](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)를 다운로드하여 만들지 않고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-135">You can download the [complete project](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="3639a-136">샘플 다운로드에는 프로젝트를 쉽게 테스트하게 하는 링크와 추가 코드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="3639a-137">샘플 다운로드 대 단계별 지침에 대한 주석을 사용하여 [이 GitHub 문제](https://github.com/aspnet/Docs/issues/6098)에서 사용자 의견을 그대로 둘 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="3639a-138">다운로드 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="3639a-138">Test the download app</span></span>

<span data-ttu-id="3639a-139">완료된 앱을 다운로드하지 않고 연습 프로젝트를 만들려는 경우 [다음 섹션](#create-a-razor-class-library)으로 건너 뜁니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-a-razor-class-library).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3639a-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3639a-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3639a-141">Visual Studio에서 *.sln* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="3639a-142">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3639a-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3639a-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3639a-144">*cli* 디렉터리의 명령 프롬프트에서 RCL 및 웹앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="3639a-145">*WebApp1* 디렉터리로 이동해 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="3639a-146">[WebApp1 테스트](#test)의 지침 준수</span><span class="sxs-lookup"><span data-stu-id="3639a-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-a-razor-class-library"></a><span data-ttu-id="3639a-147">Razor 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="3639a-147">Create a Razor Class Library</span></span>

<span data-ttu-id="3639a-148">이 섹션에서는 Razor 클래스 라이브러리(RCL)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-148">In this section, a Razor Class Library (RCL) is created.</span></span> <span data-ttu-id="3639a-149">Razor 파일이 RCL에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3639a-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3639a-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3639a-151">RCL 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-151">Create the RCL project:</span></span>

* <span data-ttu-id="3639a-152">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3639a-153">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3639a-154">앱의 이름을 **RazorUIClassLib** > **확인**합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="3639a-155">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3639a-156">**Razor 클래스 라이브러리** > **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="3639a-157">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*이라는 Razor 부분 보기 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3639a-158">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3639a-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3639a-159">명령줄에서 다음을 실행하세요.</span><span class="sxs-lookup"><span data-stu-id="3639a-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="3639a-160">이전 명령은:</span><span class="sxs-lookup"><span data-stu-id="3639a-160">The preceding commands:</span></span>

* <span data-ttu-id="3639a-161">`RazorUIClassLib` Razor 클래스 라이브러리(RCL)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-161">Creates the `RazorUIClassLib` Razor Class Library (RCL).</span></span>
* <span data-ttu-id="3639a-162">Razor _Message 페이지를 만들어 RCL에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="3639a-163">`-np` 매개 변수는 `PageModel`가 없는 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="3639a-164">만듭니다는 [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) 파일과 RCL에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="3639a-165">합니다 *_ViewStart.cshtml* 파일 (다음 섹션에 추가 됩니다)이 표시 되는 Razor 페이지 프로젝트의 레이아웃을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="3639a-166">Razor 파일 및 폴더를 프로젝트에 추가</span><span class="sxs-lookup"><span data-stu-id="3639a-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="3639a-167">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*에서 태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="3639a-168">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml*에서 태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="3639a-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`은 부분 보기(`<partial name="_Message" />`)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="3639a-170">`@addTagHelper` 지시문을 포함하지 않고 *_ViewImports.cshtml* 파일을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="3639a-171">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="3639a-172">에 대 한 자세한 *_ViewImports.cshtml*를 참조 하세요 [공유 된 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="3639a-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="3639a-173">컴파일러 오류가 없는지 확인하려면 클래스 라이브러리를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="3639a-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="3639a-174">빌드 출력은 *RazorUIClassLib.dll* 및 *RazorUIClassLib.Views.dll*을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="3639a-175">*RazorUIClassLib.Views.dll*은 컴파일된 Razor 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="3639a-176">Razor 페이지 프로젝트에서 Razor UI 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="3639a-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3639a-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3639a-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3639a-178">Razor 페이지 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="3639a-179">**솔루션 탐색기**에서 솔루션 > **추가** >  **새 프로젝트**를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="3639a-180">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3639a-181">**WebApp1** 앱 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="3639a-182">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3639a-183">**웹 응용 프로그램** > **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="3639a-184">**솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **스타트업 프로젝트로 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="3639a-185">**솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **빌드 종속성** > **프로젝트 종속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="3639a-186">**RazorUIClassLib**를 **WebApp1**의 종속성으로 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="3639a-187">**솔루션 탐색기**에서 **WebApp1**를 마우스 오른쪽 단추로 클릭하고 **추가** > **참조**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="3639a-188">**참조 관리자** 대화 상자에서 **RazorUIClassLib** > **확인**을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="3639a-189">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3639a-190">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3639a-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3639a-191">Razor 페이지 앱 및 Razor 클래스 라이브러리를 포함하는 솔루션 파일 및 Razor 페이지 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the Razor Class Library:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="3639a-192">웹앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="3639a-193">WebApp1 테스트</span><span class="sxs-lookup"><span data-stu-id="3639a-193">Test WebApp1</span></span>

<span data-ttu-id="3639a-194">Razor UI 클래스 라이브러리를 사용하고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-194">Verify the Razor UI class library is being used.</span></span>

* <span data-ttu-id="3639a-195">`/MyFeature/Page1`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="3639a-196">보기, 부분 보기 및 페이지 재정의</span><span class="sxs-lookup"><span data-stu-id="3639a-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="3639a-197">보기, 부분 보기 또는 Razor 페이지가 웹앱 및 Razor 클래스 라이브러리 모두에 있는 경우 웹앱에서 Razor 태그(*.cshtml* 파일)가 우선적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-197">When a view, partial view, or Razor Page is found in both the web app and the Razor Class Library, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="3639a-198">예를 들어, 추가 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* WebApp1, 하는 구성이 WebApp1의 Page1 Razor 클래스 라이브러리의 Page1 우선적으로 적용 됩니다 및 합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the Razor Class Library.</span></span>

<span data-ttu-id="3639a-199">샘플 다운로드에서 *WebApp1/Areas/MyFeature2*를 *WebApp1/Areas/MyFeature*로 이름을 바꾸어 우선적으로 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="3639a-200">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 보기를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="3639a-201">새 위치를 나타내기 위해 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="3639a-202">해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="3639a-203">RCL 페이지 레이아웃</span><span class="sxs-lookup"><span data-stu-id="3639a-203">RCL Pages layout</span></span>

<span data-ttu-id="3639a-204">RCL 콘텐츠 웹 앱의 일부인 것 처럼 참조 *페이지* 폴더를 다음 파일 구조로 RCL 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="3639a-205">*RazorUIClassLib/페이지*</span><span class="sxs-lookup"><span data-stu-id="3639a-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="3639a-206">*RazorUIClassLib/페이지/공유*</span><span class="sxs-lookup"><span data-stu-id="3639a-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="3639a-207">가정 *RazorUIClassLib/페이지/Shared* 부분 파일이 두: *_Header.cshtml* 하 고 *_Footer.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="3639a-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="3639a-208">합니다 `<partial>` 태그를 추가할 수 없습니다 *_Layout.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="3639a-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```
