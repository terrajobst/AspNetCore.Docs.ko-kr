---
title: '자습서: ASP.NET Core에서 Razor 페이지 시작'
author: rick-anderson
description: 이 자습서 시리즈는 ASP.NET Core에서 Razor Pages를 사용하는 방법을 보여 줍니다. 모델을 만들고, Razor Pages에 대한 코드를 생성하고, Entity Framework Core 및 SQL Server를 데이터 액세스에 사용하고, 검색 기능을 추가하고, 입력 유효성 검사를 추가하고, 마이그레이션을 사용하여 모델을 업데이트하는 방법을 알아봅니다.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b651437b698d01310f90c5f14832616c1896e6c0
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74959101"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="48262-104">자습서: ASP.NET Core에서 Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="48262-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="48262-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48262-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="48262-106">이 자습서에서는 ASP.NET Core Razor 페이지 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="48262-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="48262-107">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="48262-108">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="48262-109">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="48262-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="48262-110">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-110">Run the app.</span></span>
> * <span data-ttu-id="48262-111">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-111">Examine the project files.</span></span>

<span data-ttu-id="48262-112">이 자습서가 끝나면 나중에 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="48262-114">전제 조건</span><span class="sxs-lookup"><span data-stu-id="48262-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48262-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48262-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48262-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48262-117">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="48262-118">Razor 페이지 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="48262-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48262-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48262-120">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="48262-121">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="48262-122">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="48262-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="48262-123">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="48262-124">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="48262-125">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="48262-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="48262-126">드롭다운에서 **ASP.NET Core 3.1**을 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="48262-128">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48262-128">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48262-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48262-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="48262-131">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48262-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="48262-132">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="48262-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="48262-134">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48262-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="48262-135">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48262-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="48262-136">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 다음 대화 상자가 나타납니다. **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="48262-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="48262-137">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-137">Select **Yes**.</span></span>

  <span data-ttu-id="48262-138">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48262-139">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="48262-140">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-140">Select **File** > **New Solution**.</span></span>

![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="48262-142">**.NET Core** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="48262-144">**새 ASP.NET Core 웹 API 구성** 대화 상자에서 **대상 프레임워크**를 **.NET Core 3.1**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 선택](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="48262-146">프로젝트 이름을 **RazorPagesMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="48262-148">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="48262-148">Open the project</span></span>

<span data-ttu-id="48262-149">Visual Studio에서 **파일 > 열기**를 선택하고 *RazorPagesMovie.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="48262-150">앱 실행</span><span class="sxs-lookup"><span data-stu-id="48262-150">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="48262-151">프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="48262-151">Examine the project files</span></span>

<span data-ttu-id="48262-152">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-152">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="48262-153">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="48262-153">Pages folder</span></span>

<span data-ttu-id="48262-154">Razor 페이지 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-154">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="48262-155">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="48262-155">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="48262-156">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="48262-156">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="48262-157">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="48262-157">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="48262-158">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-158">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="48262-159">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-159">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="48262-160">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-160">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="48262-161">자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-161">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="48262-162">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="48262-162">wwwroot folder</span></span>

<span data-ttu-id="48262-163">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-163">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="48262-164">자세한 내용은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-164">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="48262-165">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="48262-165">appSettings.json</span></span>

<span data-ttu-id="48262-166">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-166">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="48262-167">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-167">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="48262-168">Program.cs</span><span class="sxs-lookup"><span data-stu-id="48262-168">Program.cs</span></span>

<span data-ttu-id="48262-169">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-169">Contains the entry point for the program.</span></span> <span data-ttu-id="48262-170">자세한 내용은 <xref:fundamentals/host/generic-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="48262-171">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="48262-171">Startup.cs</span></span>

<span data-ttu-id="48262-172">앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-172">Contains code that configures app behavior.</span></span> <span data-ttu-id="48262-173">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-173">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="48262-174">다음 단계</span><span class="sxs-lookup"><span data-stu-id="48262-174">Next steps</span></span>

<span data-ttu-id="48262-175">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-175">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="48262-176">모델 추가</span><span class="sxs-lookup"><span data-stu-id="48262-176">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="48262-177">이 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="48262-177">This is the first tutorial of a series.</span></span> <span data-ttu-id="48262-178">[시리즈](xref:tutorials/razor-pages/index)에서는 ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-178">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="48262-179">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-179">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="48262-180">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-180">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="48262-181">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="48262-181">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="48262-182">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-182">Run the app.</span></span>
> * <span data-ttu-id="48262-183">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-183">Examine the project files.</span></span>

<span data-ttu-id="48262-184">이 자습서가 끝나면 나중에 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-184">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="48262-186">전제 조건</span><span class="sxs-lookup"><span data-stu-id="48262-186">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48262-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-187">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48262-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48262-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48262-189">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="48262-190">Razor 페이지 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="48262-190">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48262-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48262-192">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="48262-193">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-193">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="48262-195">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-195">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="48262-196">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-196">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/config.png)

* <span data-ttu-id="48262-198">드롭다운에서 **ASP.NET Core 2.2**를 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-198">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="48262-200">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48262-200">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48262-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48262-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="48262-203">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48262-203">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="48262-204">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-204">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="48262-205">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-205">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="48262-206">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48262-206">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="48262-207">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48262-207">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="48262-208">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 다음 대화 상자가 나타납니다. **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="48262-208">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="48262-209">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-209">Select **Yes**.</span></span>

  <span data-ttu-id="48262-210">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-210">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48262-211">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="48262-212">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-212">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="48262-213">이전 명령은 [.NET Core CLI](/dotnet/core/tools/dotnet)를 사용하여 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48262-213">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="48262-214">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="48262-214">Open the project</span></span>

<span data-ttu-id="48262-215">Visual Studio에서 **파일 > 열기**를 선택하고 *RazorPagesMovie.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-215">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="48262-216">앱 실행</span><span class="sxs-lookup"><span data-stu-id="48262-216">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48262-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48262-218">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-218">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="48262-219">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-219">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="48262-220">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-220">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48262-221">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48262-221">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="48262-222">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-222">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="48262-223">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-223">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="48262-224">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-224">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="48262-225">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-225">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="48262-227">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="48262-227">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48262-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48262-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="48262-230">**Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-230">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="48262-231">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-231">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="48262-232">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-232">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48262-233">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48262-233">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="48262-234">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-234">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="48262-235">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="48262-236">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="48262-238">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="48262-238">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48262-240">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48262-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="48262-241">디버거 없이 실행하려면 **Cmd-Opt-F5**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="48262-241">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="48262-242">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-242">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="48262-243">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-243">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="48262-244">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="48262-244">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="48262-246">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="48262-246">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="48262-248">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="48262-248">Examine the project files</span></span>

<span data-ttu-id="48262-249">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-249">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="48262-250">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="48262-250">Pages folder</span></span>

<span data-ttu-id="48262-251">Razor 페이지 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-251">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="48262-252">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="48262-252">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="48262-253">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="48262-253">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="48262-254">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="48262-254">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="48262-255">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48262-255">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="48262-256">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-256">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="48262-257">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-257">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="48262-258">자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-258">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="48262-259">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="48262-259">wwwroot folder</span></span>

<span data-ttu-id="48262-260">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-260">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="48262-261">자세한 내용은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-261">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="48262-262">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="48262-262">appSettings.json</span></span>

<span data-ttu-id="48262-263">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-263">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="48262-264">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-264">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="48262-265">Program.cs</span><span class="sxs-lookup"><span data-stu-id="48262-265">Program.cs</span></span>

<span data-ttu-id="48262-266">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-266">Contains the entry point for the program.</span></span> <span data-ttu-id="48262-267">자세한 내용은 <xref:fundamentals/host/generic-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-267">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="48262-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="48262-268">Startup.cs</span></span>

<span data-ttu-id="48262-269">쿠키에 대한 동의 필요 여부 등 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-269">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="48262-270">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48262-270">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48262-271">추가 자료</span><span class="sxs-lookup"><span data-stu-id="48262-271">Additional resources</span></span>

* [<span data-ttu-id="48262-272">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="48262-272">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="48262-273">다음 단계</span><span class="sxs-lookup"><span data-stu-id="48262-273">Next steps</span></span>

<span data-ttu-id="48262-274">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48262-274">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="48262-275">모델 추가</span><span class="sxs-lookup"><span data-stu-id="48262-275">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
