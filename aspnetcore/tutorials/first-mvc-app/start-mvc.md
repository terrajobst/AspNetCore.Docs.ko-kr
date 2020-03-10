---
title: ASP.NET Core MVC 시작
author: rick-anderson
description: ASP.NET Core MVC를 시작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648669"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="52d68-103">ASP.NET Core MVC 시작</span><span class="sxs-lookup"><span data-stu-id="52d68-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="52d68-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="52d68-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="52d68-105">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="52d68-106">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="52d68-107">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="52d68-108">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="52d68-108">Create a web app.</span></span>
> * <span data-ttu-id="52d68-109">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="52d68-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="52d68-110">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="52d68-110">Work with a database.</span></span>
> * <span data-ttu-id="52d68-111">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="52d68-111">Add search and validation.</span></span>

<span data-ttu-id="52d68-112">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="52d68-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="52d68-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52d68-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="52d68-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="52d68-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="52d68-116">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="52d68-117">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="52d68-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52d68-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="52d68-119">Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="52d68-120">**ASP.NET Core 웹 응용 프로그램**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="52d68-122">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="52d68-123">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie**로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)

* <span data-ttu-id="52d68-125">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="52d68-126">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="52d68-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="52d68-127">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="52d68-128">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="52d68-129">이 프로젝트가 기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="52d68-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="52d68-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="52d68-131">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="52d68-132">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d68-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="52d68-133">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="52d68-134">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="52d68-135">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="52d68-136">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="52d68-137">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-137">Select **Yes**</span></span>

  * <span data-ttu-id="52d68-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="52d68-139">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="52d68-140">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="52d68-141">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-141">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="52d68-143">**.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="52d68-145">**새 ASP.NET Core Web API 구성** 대화 상자에서 **.NET Core 3.1**의 **대상 프레임워크**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 선택](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="52d68-147">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="52d68-148">앱 실행</span><span class="sxs-lookup"><span data-stu-id="52d68-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52d68-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="52d68-150">**Ctrl-F5**를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="52d68-151">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="52d68-152">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="52d68-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="52d68-153">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="52d68-154">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="52d68-155">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="52d68-156">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="52d68-157">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="52d68-159">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="52d68-161">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-161">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="52d68-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="52d68-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="52d68-164">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="52d68-165">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="52d68-166">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="52d68-167">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="52d68-168">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="52d68-169">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="52d68-170">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="52d68-172">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="52d68-173">**실행** > **디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="52d68-174">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="52d68-175">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="52d68-176">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="52d68-177">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="52d68-178">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="52d68-179">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="52d68-180">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-180">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="52d68-182">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="52d68-183">다음</span><span class="sxs-lookup"><span data-stu-id="52d68-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="52d68-184">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="52d68-185">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="52d68-186">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="52d68-187">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="52d68-187">Create a web app.</span></span>
> * <span data-ttu-id="52d68-188">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="52d68-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="52d68-189">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="52d68-189">Work with a database.</span></span>
> * <span data-ttu-id="52d68-190">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="52d68-190">Add search and validation.</span></span>

<span data-ttu-id="52d68-191">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="52d68-192">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="52d68-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52d68-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="52d68-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="52d68-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="52d68-195">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="52d68-196">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="52d68-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52d68-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="52d68-198">Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="52d68-199">**ASP.NET Core 웹 응용 프로그램**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="52d68-201">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="52d68-202">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie**로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)


* <span data-ttu-id="52d68-204">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="52d68-205">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="52d68-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="52d68-206">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="52d68-207">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="52d68-208">다음은 기본 시작 프로젝트이며 여기서 시작하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="52d68-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="52d68-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="52d68-210">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="52d68-211">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d68-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="52d68-212">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="52d68-213">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="52d68-214">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="52d68-215">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="52d68-216">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-216">Select **Yes**</span></span>

  * <span data-ttu-id="52d68-217">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="52d68-218">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="52d68-219">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="52d68-220">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-220">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="52d68-222">**.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="52d68-224">**새 ASP.NET Core Web API 구성** 대화 상자에서 **.NET Core 2.2**라는 기본 **대상 프레임워크**를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 선택](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="52d68-226">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="52d68-227">앱 실행</span><span class="sxs-lookup"><span data-stu-id="52d68-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52d68-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="52d68-229">**Ctrl-F5**를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="52d68-230">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="52d68-231">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="52d68-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="52d68-232">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="52d68-233">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="52d68-234">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="52d68-235">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="52d68-236">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="52d68-238">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="52d68-240">**Accept**를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="52d68-241">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-241">This app doesn't track personal information.</span></span> <span data-ttu-id="52d68-242">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="52d68-244">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-244">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="52d68-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="52d68-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="52d68-247">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="52d68-248">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="52d68-249">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="52d68-250">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="52d68-251">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="52d68-252">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="52d68-253">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="52d68-254">**Accept**를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="52d68-255">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-255">This app doesn't track personal information.</span></span> <span data-ttu-id="52d68-256">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="52d68-258">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-258">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="52d68-260">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52d68-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="52d68-261">**실행** > **디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="52d68-262">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="52d68-263">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="52d68-264">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="52d68-265">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="52d68-266">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="52d68-267">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="52d68-268">**Accept**를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="52d68-269">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-269">This app doesn't track personal information.</span></span> <span data-ttu-id="52d68-270">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="52d68-272">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-272">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="52d68-274">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="52d68-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="52d68-275">다음</span><span class="sxs-lookup"><span data-stu-id="52d68-275">Next</span></span>](adding-controller.md)

::: moniker-end
