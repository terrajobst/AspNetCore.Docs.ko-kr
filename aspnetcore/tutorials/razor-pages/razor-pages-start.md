---
title: '자습서: ASP.NET Core에서 Razor 페이지 시작'
author: rick-anderson
description: 이 자습서 시리즈는 ASP.NET Core에서 Razor Pages를 사용하는 방법을 보여 줍니다. 모델을 만들고, Razor Pages에 대한 코드를 생성하고, Entity Framework Core 및 SQL Server를 데이터 액세스에 사용하고, 검색 기능을 추가하고, 입력 유효성 검사를 추가하고, 마이그레이션을 사용하여 모델을 업데이트하는 방법을 알아봅니다.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1d264ca4a605d8291e273a8f054c92e7eefa5548
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468849"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="268fb-104">자습서: ASP.NET Core에서 Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="268fb-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="268fb-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="268fb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="268fb-106">이 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="268fb-107">[시리즈](xref:tutorials/razor-pages/index)에서는 ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="268fb-108">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="268fb-109">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="268fb-110">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="268fb-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="268fb-111">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-111">Run the app.</span></span>
> * <span data-ttu-id="268fb-112">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-112">Examine the project files.</span></span>

<span data-ttu-id="268fb-113">이 자습서가 끝나면 나중에 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="268fb-115">Razor 페이지 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="268fb-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="268fb-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="268fb-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="268fb-117">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="268fb-118">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="268fb-119">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="268fb-120">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="268fb-122">드롭다운에서 **ASP.NET Core 2.2**를 선택한 다음, **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="268fb-124">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-124">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="268fb-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="268fb-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="268fb-127">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="268fb-128">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="268fb-129">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="268fb-130">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="268fb-131">`code` 명령은 Visual Studio Code의 새 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="268fb-132">다음과 같은 대화 상자가 표시됩니다. **빌드 및 디버그에 필요한 자산이 'RazorPagesMovie'에서 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="268fb-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="268fb-133">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-133">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="268fb-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="268fb-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="268fb-135">터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-135">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="268fb-136">이전 명령은 [.NET Core CLI](/dotnet/core/tools/dotnet)를 사용하여 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="268fb-137">프로젝트 열기</span><span class="sxs-lookup"><span data-stu-id="268fb-137">Open the project</span></span>

<span data-ttu-id="268fb-138">Visual Studio에서 **파일 > 열기**를 선택하고 *RazorPagesMovie.csproj* 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="268fb-139">앱 실행</span><span class="sxs-lookup"><span data-stu-id="268fb-139">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="268fb-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="268fb-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="268fb-141">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-141">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="268fb-142">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="268fb-143">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="268fb-144">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="268fb-145">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="268fb-146">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="268fb-147">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-147">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="268fb-148">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-148">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="268fb-150">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-150">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="268fb-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="268fb-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="268fb-153">**Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="268fb-154">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="268fb-155">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="268fb-156">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="268fb-157">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-157">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="268fb-158">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-158">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="268fb-159">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-159">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="268fb-161">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-161">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="268fb-163">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="268fb-163">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="268fb-164">디버거 없이 실행하려면 **Cmd-Opt-F5**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-164">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="268fb-165">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-165">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="268fb-166">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-166">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="268fb-167">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-167">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="268fb-169">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-169">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="268fb-171">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="268fb-171">Examine the project files</span></span>

<span data-ttu-id="268fb-172">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-172">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="268fb-173">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="268fb-173">Pages folder</span></span>

<span data-ttu-id="268fb-174">Razor 페이지 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-174">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="268fb-175">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-175">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="268fb-176">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="268fb-176">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="268fb-177">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="268fb-177">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="268fb-178">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-178">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="268fb-179">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-179">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="268fb-180">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-180">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="268fb-181">자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="268fb-181">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="268fb-182">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="268fb-182">wwwroot folder</span></span>

<span data-ttu-id="268fb-183">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-183">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="268fb-184">자세한 내용은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="268fb-184">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="268fb-185">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="268fb-185">appSettings.json</span></span>

<span data-ttu-id="268fb-186">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-186">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="268fb-187">자세한 내용은 <xref:fundamentals/configuration/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="268fb-187">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="268fb-188">Program.cs</span><span class="sxs-lookup"><span data-stu-id="268fb-188">Program.cs</span></span>

<span data-ttu-id="268fb-189">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-189">Contains the entry point for the program.</span></span> <span data-ttu-id="268fb-190">자세한 내용은 <xref:fundamentals/host/web-host>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="268fb-190">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="268fb-191">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="268fb-191">Startup.cs</span></span>

<span data-ttu-id="268fb-192">쿠키에 대한 동의 필요 여부 등 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-192">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="268fb-193">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="268fb-193">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="268fb-194">추가 자료</span><span class="sxs-lookup"><span data-stu-id="268fb-194">Additional resources</span></span>

* [<span data-ttu-id="268fb-195">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="268fb-195">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="268fb-196">다음 단계</span><span class="sxs-lookup"><span data-stu-id="268fb-196">Next steps</span></span>

<span data-ttu-id="268fb-197">이 자습서에서는 다음을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="268fb-198">Razor Pages 웹앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-198">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="268fb-199">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-199">Ran the app.</span></span>
> * <span data-ttu-id="268fb-200">프로젝트 파일을 검사했습니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-200">Examined the project files.</span></span>

<span data-ttu-id="268fb-201">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="268fb-201">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="268fb-202">모델 추가</span><span class="sxs-lookup"><span data-stu-id="268fb-202">Add a model</span></span>](xref:tutorials/razor-pages/model)
