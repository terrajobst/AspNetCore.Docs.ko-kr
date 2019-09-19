---
title: '자습서: ASP.NET Core에서 Razor 페이지 시작'
author: rick-anderson
description: 이 자습서 시리즈는 ASP.NET Core에서 Razor Pages를 사용하는 방법을 보여 줍니다. 모델을 만들고, Razor Pages에 대한 코드를 생성하고, Entity Framework Core 및 SQL Server를 데이터 액세스에 사용하고, 검색 기능을 추가하고, 입력 유효성 검사를 추가하고, 마이그레이션을 사용하여 모델을 업데이트하는 방법을 알아봅니다.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 0cc00cb85b6054752417b82c783cfd4c306aeda5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082572"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="57dc5-104">자습서: ASP.NET Core에서 Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="57dc5-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="57dc5-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="57dc5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="57dc5-106">이 자습서에서는 ASP.NET Core Razor 페이지 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="57dc5-107">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="57dc5-108">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57dc5-109">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="57dc5-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="57dc5-110">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-110">Run the app.</span></span>
> * <span data-ttu-id="57dc5-111">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-111">Examine the project files.</span></span>

<span data-ttu-id="57dc5-112">이 자습서가 끝나면 나중에 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="57dc5-114">전제 조건</span><span class="sxs-lookup"><span data-stu-id="57dc5-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57dc5-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57dc5-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57dc5-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57dc5-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="57dc5-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57dc5-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="57dc5-118">Razor 페이지 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="57dc5-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57dc5-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57dc5-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57dc5-120">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="57dc5-121">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="57dc5-122">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="57dc5-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="57dc5-123">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="57dc5-124">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="57dc5-125">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="57dc5-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="57dc5-126">드롭다운에서 **ASP.NET Core 3.0**을 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="57dc5-128">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-128">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57dc5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57dc5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="57dc5-131">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="57dc5-132">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="57dc5-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="57dc5-134">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="57dc5-135">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="57dc5-136">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 다음 대화 상자가 나타납니다. **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="57dc5-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="57dc5-137">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-137">Select **Yes**.</span></span>

  <span data-ttu-id="57dc5-138">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="57dc5-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57dc5-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="57dc5-140">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-140">Select **File** > **New Solution**.</span></span>

![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="57dc5-142">**.NET Core** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="57dc5-144">**새 ASP.NET Core 웹 API 구성** 대화 상자에서 **대상 프레임워크**를 **.NET Core 3.0**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![macOS .NET Core 3.0 선택](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="57dc5-146">프로젝트 이름을 **RazorPagesMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="57dc5-148">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="57dc5-148">Open the project</span></span>

<span data-ttu-id="57dc5-149">Visual Studio에서 **파일 > 열기**를 선택하고 *RazorPagesMovie.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="57dc5-150">앱 실행</span><span class="sxs-lookup"><span data-stu-id="57dc5-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57dc5-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57dc5-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57dc5-152">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="57dc5-153">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57dc5-154">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57dc5-155">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="57dc5-156">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="57dc5-157">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57dc5-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57dc5-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="57dc5-159">**Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="57dc5-160">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="57dc5-161">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57dc5-162">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57dc5-163">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="57dc5-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57dc5-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57dc5-165">디버거 없이 실행하려면 **Alt-Cmd-Enter**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="57dc5-166">또는 메뉴 모음에서 실행>디버깅하지 않고 시작으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="57dc5-167">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="57dc5-168">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="57dc5-168">Examine the project files</span></span>

<span data-ttu-id="57dc5-169">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="57dc5-170">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="57dc5-170">Pages folder</span></span>

<span data-ttu-id="57dc5-171">Razor 페이지 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="57dc5-172">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="57dc5-173">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="57dc5-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="57dc5-174">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="57dc5-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="57dc5-175">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="57dc5-176">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="57dc5-177">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="57dc5-178">자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="57dc5-179">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="57dc5-179">wwwroot folder</span></span>

<span data-ttu-id="57dc5-180">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="57dc5-181">자세한 내용은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="57dc5-182">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="57dc5-182">appSettings.json</span></span>

<span data-ttu-id="57dc5-183">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="57dc5-184">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="57dc5-185">Program.cs</span><span class="sxs-lookup"><span data-stu-id="57dc5-185">Program.cs</span></span>

<span data-ttu-id="57dc5-186">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-186">Contains the entry point for the program.</span></span> <span data-ttu-id="57dc5-187">자세한 내용은 <xref:fundamentals/host/generic-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="57dc5-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="57dc5-188">Startup.cs</span></span>

<span data-ttu-id="57dc5-189">앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="57dc5-190">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="57dc5-191">다음 단계</span><span class="sxs-lookup"><span data-stu-id="57dc5-191">Next steps</span></span>

<span data-ttu-id="57dc5-192">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57dc5-193">모델 추가</span><span class="sxs-lookup"><span data-stu-id="57dc5-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="57dc5-194">이 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="57dc5-195">[시리즈](xref:tutorials/razor-pages/index)에서는 ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="57dc5-196">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="57dc5-197">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="57dc5-198">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="57dc5-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="57dc5-199">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-199">Run the app.</span></span>
> * <span data-ttu-id="57dc5-200">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-200">Examine the project files.</span></span>

<span data-ttu-id="57dc5-201">이 자습서가 끝나면 나중에 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="57dc5-203">전제 조건</span><span class="sxs-lookup"><span data-stu-id="57dc5-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57dc5-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57dc5-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57dc5-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57dc5-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="57dc5-206">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57dc5-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="57dc5-207">Razor 페이지 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="57dc5-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57dc5-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57dc5-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57dc5-209">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="57dc5-210">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="57dc5-212">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="57dc5-213">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/config.png)

* <span data-ttu-id="57dc5-215">드롭다운에서 **ASP.NET Core 2.2**를 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="57dc5-217">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-217">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57dc5-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57dc5-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="57dc5-220">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="57dc5-221">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="57dc5-222">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-222">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="57dc5-223">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="57dc5-224">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="57dc5-225">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 다음 대화 상자가 나타납니다. **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="57dc5-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="57dc5-226">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-226">Select **Yes**.</span></span>

  <span data-ttu-id="57dc5-227">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="57dc5-228">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57dc5-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57dc5-229">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="57dc5-230">이전 명령은 [.NET Core CLI](/dotnet/core/tools/dotnet)를 사용하여 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="57dc5-231">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="57dc5-231">Open the project</span></span>

<span data-ttu-id="57dc5-232">Visual Studio에서 **파일 > 열기**를 선택하고 *RazorPagesMovie.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="57dc5-233">앱 실행</span><span class="sxs-lookup"><span data-stu-id="57dc5-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="57dc5-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57dc5-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57dc5-235">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="57dc5-236">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="57dc5-237">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57dc5-238">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="57dc5-239">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="57dc5-240">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="57dc5-241">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="57dc5-242">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="57dc5-244">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-244">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="57dc5-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57dc5-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="57dc5-247">**Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="57dc5-248">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="57dc5-249">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="57dc5-250">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="57dc5-251">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="57dc5-252">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="57dc5-253">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="57dc5-255">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-255">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="57dc5-257">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="57dc5-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="57dc5-258">디버거 없이 실행하려면 **Cmd-Opt-F5**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="57dc5-259">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="57dc5-260">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="57dc5-261">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="57dc5-263">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-263">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="57dc5-265">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="57dc5-265">Examine the project files</span></span>

<span data-ttu-id="57dc5-266">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="57dc5-267">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="57dc5-267">Pages folder</span></span>

<span data-ttu-id="57dc5-268">Razor 페이지 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="57dc5-269">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="57dc5-270">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="57dc5-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="57dc5-271">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="57dc5-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="57dc5-272">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="57dc5-273">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="57dc5-274">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="57dc5-275">자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="57dc5-276">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="57dc5-276">wwwroot folder</span></span>

<span data-ttu-id="57dc5-277">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="57dc5-278">자세한 내용은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="57dc5-279">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="57dc5-279">appSettings.json</span></span>

<span data-ttu-id="57dc5-280">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="57dc5-281">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="57dc5-282">Program.cs</span><span class="sxs-lookup"><span data-stu-id="57dc5-282">Program.cs</span></span>

<span data-ttu-id="57dc5-283">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-283">Contains the entry point for the program.</span></span> <span data-ttu-id="57dc5-284">자세한 내용은 <xref:fundamentals/host/generic-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="57dc5-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="57dc5-285">Startup.cs</span></span>

<span data-ttu-id="57dc5-286">쿠키에 대한 동의 필요 여부 등 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="57dc5-287">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57dc5-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57dc5-288">추가 자료</span><span class="sxs-lookup"><span data-stu-id="57dc5-288">Additional resources</span></span>

* [<span data-ttu-id="57dc5-289">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="57dc5-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="57dc5-290">다음 단계</span><span class="sxs-lookup"><span data-stu-id="57dc5-290">Next steps</span></span>

<span data-ttu-id="57dc5-291">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="57dc5-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="57dc5-292">모델 추가</span><span class="sxs-lookup"><span data-stu-id="57dc5-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
