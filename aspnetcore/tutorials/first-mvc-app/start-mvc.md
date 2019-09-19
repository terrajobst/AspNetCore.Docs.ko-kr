---
title: ASP.NET Core MVC 시작
author: rick-anderson
description: ASP.NET Core MVC를 시작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 08/05/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 52d8fc631075408991d3a2e62cdaccf681c5927f
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082152"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="48ff6-103">ASP.NET Core MVC 시작</span><span class="sxs-lookup"><span data-stu-id="48ff6-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="48ff6-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48ff6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="48ff6-105">이 자습서에서는 ASP.NET Core MVC 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="48ff6-106">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="48ff6-107">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="48ff6-108">웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-108">Create a web app.</span></span>
> * <span data-ttu-id="48ff6-109">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="48ff6-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="48ff6-110">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="48ff6-110">Work with a database.</span></span>
> * <span data-ttu-id="48ff6-111">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="48ff6-111">Add search and validation.</span></span>

<span data-ttu-id="48ff6-112">마지막으로 동영상 데이터를 관리하고 표시할 수 있는 앱이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="48ff6-113">전제 조건</span><span class="sxs-lookup"><span data-stu-id="48ff6-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48ff6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ff6-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48ff6-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ff6-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48ff6-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="48ff6-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="48ff6-117">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="48ff6-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48ff6-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ff6-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48ff6-119">Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="48ff6-120">**ASP.NET Core 웹 애플리케이션**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![새 ASP.NET Core 웹 애플리케이션](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="48ff6-122">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="48ff6-123">프로젝트 이름을 **MvcMovie**로 지정하는 것이 중요하므로 코드를 복사할 때 네임스페이스가 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](start-mvc/_static/config.png)

* <span data-ttu-id="48ff6-125">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="48ff6-126">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="48ff6-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="48ff6-127">Visual Studio에서는 방금 만든 MVC 프로젝트에 대한 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="48ff6-128">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하면 바로 앱이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="48ff6-129">이것은 기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48ff6-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ff6-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ff6-131">이 자습서에서는 VS 코드를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="48ff6-132">자세한 내용은 [VS 코드 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48ff6-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="48ff6-133">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="48ff6-134">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="48ff6-135">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="48ff6-136">**이 있는 대화 상자가 나타나고 'MvcMovie'에서 빌드 및 디버그에 필요한 자산이 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="48ff6-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="48ff6-137">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-137">Select **Yes**</span></span>

  * <span data-ttu-id="48ff6-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 포로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="48ff6-139">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48ff6-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="48ff6-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="48ff6-141">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-141">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="48ff6-143">**.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="48ff6-145">**새 ASP.NET Core Web API 구성** 대화 상자에서 **.NET Core 3.0**의 **대상 프레임워크**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.0**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="48ff6-146">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="48ff6-147">앱 실행</span><span class="sxs-lookup"><span data-stu-id="48ff6-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48ff6-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ff6-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48ff6-149">**Ctrl-F5**를 선택하여 비디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="48ff6-150">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="48ff6-151">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않음을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ff6-152">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ff6-153">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="48ff6-154">Ctrl+F5(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ff6-155">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="48ff6-156">**디버그** 메뉴 항목에서 앱을 디버그 또는 디버그 이외 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="48ff6-158">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="48ff6-160">다음 이미지에 앱이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-160">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48ff6-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ff6-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ff6-163">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="48ff6-164">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="48ff6-165">주소 표시줄에 `localhost:port:5001`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="48ff6-166">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="48ff6-167">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="48ff6-168">Ctrl+F5(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ff6-169">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48ff6-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="48ff6-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="48ff6-172">**실행** > **디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-172">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="48ff6-173">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-173">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="48ff6-174">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-174">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ff6-175">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-175">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ff6-176">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-176">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="48ff6-177">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-177">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="48ff6-178">**실행** 메뉴 항목에서 앱을 디버그 또는 디버그 이외 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-178">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="48ff6-179">다음 이미지에 앱이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-179">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="48ff6-181">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 일부 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-181">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="48ff6-182">다음</span><span class="sxs-lookup"><span data-stu-id="48ff6-182">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="48ff6-183">이 자습서에서는 ASP.NET Core MVC 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-183">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="48ff6-184">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-184">The app manages a database of movie titles.</span></span> <span data-ttu-id="48ff6-185">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-185">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="48ff6-186">웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-186">Create a web app.</span></span>
> * <span data-ttu-id="48ff6-187">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="48ff6-187">Add and scaffold a model.</span></span>
> * <span data-ttu-id="48ff6-188">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="48ff6-188">Work with a database.</span></span>
> * <span data-ttu-id="48ff6-189">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="48ff6-189">Add search and validation.</span></span>

<span data-ttu-id="48ff6-190">마지막으로 동영상 데이터를 관리하고 표시할 수 있는 앱이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-190">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="48ff6-191">전제 조건</span><span class="sxs-lookup"><span data-stu-id="48ff6-191">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48ff6-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ff6-192">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48ff6-193">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ff6-193">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48ff6-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="48ff6-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="48ff6-195">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="48ff6-195">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48ff6-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ff6-196">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48ff6-197">Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-197">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="48ff6-198">**ASP.NET Core 웹 애플리케이션**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-198">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![새 ASP.NET Core 웹 애플리케이션](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="48ff6-200">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-200">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="48ff6-201">프로젝트 이름을 **MvcMovie**로 지정하는 것이 중요하므로 코드를 복사할 때 네임스페이스가 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-201">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 애플리케이션](start-mvc/_static/config.png)


* <span data-ttu-id="48ff6-203">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-203">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="48ff6-204">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="48ff6-204">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="48ff6-205">Visual Studio에서는 방금 만든 MVC 프로젝트에 대한 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-205">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="48ff6-206">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하면 바로 앱이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-206">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="48ff6-207">다음은 기본 시작 프로젝트이며 여기서 시작하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-207">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48ff6-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ff6-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ff6-209">이 자습서에서는 VS 코드를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-209">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="48ff6-210">자세한 내용은 [VS 코드 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="48ff6-210">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="48ff6-211">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-211">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="48ff6-212">디렉터리(`cd`)를 프로젝트를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-212">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="48ff6-213">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-213">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="48ff6-214">**이 있는 대화 상자가 나타나고 'MvcMovie'에서 빌드 및 디버그에 필요한 자산이 누락되었습니다. 추가할까요?**</span><span class="sxs-lookup"><span data-stu-id="48ff6-214">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="48ff6-215">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-215">Select **Yes**</span></span>

  * <span data-ttu-id="48ff6-216">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 포로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-216">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="48ff6-217">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-217">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48ff6-218">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="48ff6-218">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="48ff6-219">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-219">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="48ff6-221">**.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-221">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="48ff6-223">**새 ASP.NET Core Web API 구성** 대화 상자에서 **.NET Core 2.2**라는 기본 **대상 프레임워크**를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-223">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 선택](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="48ff6-225">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-225">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="48ff6-226">앱 실행</span><span class="sxs-lookup"><span data-stu-id="48ff6-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="48ff6-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48ff6-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48ff6-228">**Ctrl-F5**를 선택하여 비디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-228">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="48ff6-229">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="48ff6-230">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않음을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-230">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ff6-231">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-231">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ff6-232">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-232">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="48ff6-233">Ctrl+F5(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-233">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ff6-234">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-234">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="48ff6-235">**디버그** 메뉴 항목에서 앱을 디버그 또는 디버그 이외 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-235">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="48ff6-237">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-237">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="48ff6-239">**승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-239">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="48ff6-240">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-240">This app doesn't track personal information.</span></span> <span data-ttu-id="48ff6-241">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 충족할 수 있도록 자산을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-241">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="48ff6-243">다음 이미지에서는 추적을 승인한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-243">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="48ff6-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48ff6-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48ff6-246">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-246">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="48ff6-247">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-247">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="48ff6-248">주소 표시줄에 `localhost:port:5001`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-248">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="48ff6-249">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-249">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="48ff6-250">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-250">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="48ff6-251">Ctrl+F5(디버그 이외 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-251">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="48ff6-252">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-252">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="48ff6-253">**승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-253">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="48ff6-254">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-254">This app doesn't track personal information.</span></span> <span data-ttu-id="48ff6-255">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 충족할 수 있도록 자산을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-255">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="48ff6-257">다음 이미지에서는 추적을 승인한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-257">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="48ff6-259">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="48ff6-259">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="48ff6-260">**실행** > **디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-260">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="48ff6-261">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-261">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="48ff6-262">주소 표시줄에 `localhost:port#`이 표시되고 `example.com` 등은 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-262">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="48ff6-263">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-263">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="48ff6-264">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에는 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-264">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="48ff6-265">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-265">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="48ff6-266">**실행** 메뉴 항목에서 앱을 디버그 또는 디버그 이외 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-266">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="48ff6-267">**승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-267">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="48ff6-268">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-268">This app doesn't track personal information.</span></span> <span data-ttu-id="48ff6-269">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 충족할 수 있도록 자산을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-269">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="48ff6-271">다음 이미지에서는 추적을 승인한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-271">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="48ff6-273">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 일부 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="48ff6-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="48ff6-274">다음</span><span class="sxs-lookup"><span data-stu-id="48ff6-274">Next</span></span>](adding-controller.md)

::: moniker-end
