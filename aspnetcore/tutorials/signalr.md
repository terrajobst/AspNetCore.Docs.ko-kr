---
title: ASP.NET Core SignalR로 시작
author: bradygaster
description: 이 자습서에서는 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/signalr
ms.openlocfilehash: 078f1875d22a90f90575826e6f212205cd4b3d5b
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519198"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="f292e-103">자습서: ASP.NET Core SignalR 시작</span><span class="sxs-lookup"><span data-stu-id="f292e-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f292e-104">본 자습서에서는 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="f292e-105">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f292e-106">웹 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-106">Create a web project.</span></span>
> * <span data-ttu-id="f292e-107">SignalR 클라이언트 라이브러리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="f292e-108">SignalR 허브를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="f292e-109">SignalR을 사용하도록 프로젝트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="f292e-110">모든 클라이언트에서 연결된 모든 클라이언트로 메시지를 보내는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="f292e-111">이 모든 과정을 마치면 동작하는 채팅 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-111">At the end, you'll have a working chat app:</span></span>

![SignalR 샘플 앱](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="f292e-113">전제 조건</span><span class="sxs-lookup"><span data-stu-id="f292e-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="f292e-117">웹앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="f292e-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="f292e-119">메뉴에서 **파일 > 새 프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="f292e-120">**새 프로젝트 만들기** 대화 상자에서 **ASP.NET Core 웹 애플리케이션**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="f292e-121">**새 프로젝트 구성** 대화 상자에서 *SignalRChat* 프로젝트 이름을 지정한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="f292e-122">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.0**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="f292e-123">Razor Pages를 사용하는 프로젝트를 생성하려면 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="f292e-126">새 프로젝트 폴더를 만들 폴더에 대한 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="f292e-127">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f292e-129">메뉴에서 **파일 > 새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="f292e-130">**.NET Core > 앱 > 웹 애플리케이션** (**웹 애플리케이션(Model-View-Controller)** 선택 안 함)을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="f292e-131">**대상 프레임워크**가 **.NET Core 3.0**으로 설정되어 있는지 확인한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="f292e-132">프로젝트 이름을 *SignalRChat*로 지정한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f292e-133">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="f292e-133">Add the SignalR client library</span></span>

<span data-ttu-id="f292e-134">SignalR 서버 라이브러리는 ASP.NET Core 3.0 공유 프레임워크에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="f292e-135">JavaScript 클라이언트 라이브러리는 프로젝트에 자동으로 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="f292e-136">본 자습서에서는 라이브러리 관리자(LibMan)를 사용하여 *unpkg*에서 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="f292e-137">unpkg는 Node.js의 패키지 관리자인 npm에서 찾은 모든 내용을 전달할 수 있는 콘텐츠 배달 네트워크(CDN, Content Delivery Network)입니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="f292e-139">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **클라이언트 쪽 라이브러리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="f292e-140">**클라이언트 쪽 라이브러리 추가** 대화 상자에서 **공급자**로 **unpkg**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="f292e-141">**라이브러리**인 경우 `@microsoft/signalr@latest`를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="f292e-142">**특정 파일 선택**을 선택하고 *dist/browser* 폴더를 확장한 다음 *signalr.js* 및 *signalr.min.js*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="f292e-143">**대상 위치**를 *wwwroot/js/signalr/* 로 설정하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 라이브러리 선택](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="f292e-145">그러면 LibMan이 *wwwroot/js/signalr* 폴더를 생성한 다음 여기에 선택한 파일을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="f292e-147">통합 터미널에서 다음 명령을 실행하여 LibMan을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="f292e-148">다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="f292e-149">출력이 표시되기 전에 잠시 기다려야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="f292e-150">매개 변수는 다음 옵션을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="f292e-151">unpkg 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="f292e-152">파일을 *wwwroot/js/signalr* 대상으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="f292e-153">지정된 파일만 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-153">Copy only the specified files.</span></span>

  <span data-ttu-id="f292e-154">출력은 다음 예와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f292e-156">**터미널**에서 다음 명령을 실행하여 LibMan을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="f292e-157">프로젝트 폴더로 이동합니다(*SignalRChat.csproj* 파일을 포함하는 폴더).</span><span class="sxs-lookup"><span data-stu-id="f292e-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="f292e-158">다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="f292e-159">매개 변수는 다음 옵션을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="f292e-160">unpkg 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="f292e-161">파일을 *wwwroot/js/signalr* 대상으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="f292e-162">지정된 파일만 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-162">Copy only the specified files.</span></span>

  <span data-ttu-id="f292e-163">출력은 다음 예와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="f292e-164">SignalR 허브 만들기</span><span class="sxs-lookup"><span data-stu-id="f292e-164">Create a SignalR hub</span></span>

<span data-ttu-id="f292e-165">*허브*는 클라이언트-서버 통신을 처리하는 높은 수준의 파이프라인으로 제공되는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="f292e-166">SignalRChat 프로젝트 폴더에서 *Hubs* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="f292e-167">*Hubs* 폴더에 다음 코드를 사용하여 *ChatHub.cs* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="f292e-168">`ChatHub` 클래스는 SignalR `Hub` 클래스에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="f292e-169">`Hub` 클래스는 연결, 그룹 및 메시징을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="f292e-170">연결된 클라이언트에서 `SendMessage` 메서드를 호출하여 모든 클라이언트에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="f292e-171">메서드를 호출하는 JavaScript 클라이언트 코드는 자습서 뒷부분에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="f292e-172">SignalR 코드는 최대한의 확장성을 제공할 수 있도록 비동기적입니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="f292e-173">SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="f292e-173">Configure SignalR</span></span>

<span data-ttu-id="f292e-174">SignalR 서버는 SignalR에 SignalR 요청을 전달하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="f292e-175">다음 강조 표시된 코드를 *Startup.cs* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="f292e-176">이러한 변경 사항은 ASP.NET Core 종속성 주입 및 라우팅 시스템에 SignalR을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="f292e-177">SignalR 클라이언트 코드 추가</span><span class="sxs-lookup"><span data-stu-id="f292e-177">Add SignalR client code</span></span>

* <span data-ttu-id="f292e-178">*Pages\Index.cshtml*의 콘텐츠를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="f292e-179">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="f292e-179">The preceding code:</span></span>

  * <span data-ttu-id="f292e-180">이름 및 메시지 텍스트에 대한 텍스트 상자 및 전송 단추를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="f292e-181">SignalR 허브에서 받은 메시지를 표시하기 위해 `id="messagesList"`를 사용하여 목록을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="f292e-182">SignalR에 대한 스크립트 참조 및 다음 단계에서 만드는 *chat.js* 애플리케이션 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="f292e-183">*wwwroot/js* 폴더에서 다음 코드를 사용하여 *chat.js* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="f292e-184">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="f292e-184">The preceding code:</span></span>

  * <span data-ttu-id="f292e-185">연결을 만들고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="f292e-186">허브에 메시지를 전송하는 처리기를 전송 단추에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="f292e-187">허브에서 메시지를 수신하고 목록에 추가하는 처리기를 연결 개체에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="f292e-188">앱 실행</span><span class="sxs-lookup"><span data-stu-id="f292e-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f292e-190">**CTRL+F5** 키를 눌러 디버깅 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f292e-192">통합 터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f292e-194">메뉴에서 **실행 > 디버깅하지 않고 시작**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="f292e-195">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="f292e-196">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기 메시지** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="f292e-197">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR 샘플 앱](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="f292e-199">앱이 작동하지 않는 경우 브라우저 개발자 도구(F12)를 열고 콘솔로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="f292e-200">HTML 및 JavaScript 코드와 관련된 오류를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="f292e-201">예를 들어 지정되지 않은 다른 폴더에 *signalr.js*를 넣었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="f292e-202">이 경우 해당 파일에 대한 참조는 작동하지 않으며 콘솔에 404 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="f292e-203">![signalr.js 찾을 수 없음 오류](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="f292e-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="f292e-204">Chrome에서 ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY 오류가 발생하는 경우, 다음 명령을 실행하여 개발 인증서를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="f292e-205">다음 단계</span><span class="sxs-lookup"><span data-stu-id="f292e-205">Next steps</span></span>

<span data-ttu-id="f292e-206">SignalR에 대해 자세히 알아보려면 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f292e-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f292e-207">ASP.NET Core SignalR 소개</span><span class="sxs-lookup"><span data-stu-id="f292e-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f292e-208">본 자습서에서는 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="f292e-209">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-209">You learn how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="f292e-210">웹 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-210">Create a web project.</span></span>   
> * <span data-ttu-id="f292e-211">SignalR 클라이언트 라이브러리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-211">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="f292e-212">SignalR 허브를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-212">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="f292e-213">SignalR을 사용하도록 프로젝트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-213">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="f292e-214">모든 클라이언트에서 연결된 모든 클라이언트로 메시지를 보내는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-214">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="f292e-215">이 모든 과정을 마치면 동작하는 채팅 앱이 만들어집니다. ![SignalR 샘플 앱](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="f292e-215">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="f292e-216">전제 조건</span><span class="sxs-lookup"><span data-stu-id="f292e-216">Prerequisites</span></span>    

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-217">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-218">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-218">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-219">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="f292e-220">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="f292e-220">Create a web project</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-221">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="f292e-222">메뉴에서 **파일 > 새 프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-222">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="f292e-223">**새 프로젝트** 대화 상자에서 **설치됨 &gt; Visual C# &gt; 웹 &gt; ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-223">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f292e-224">프로젝트의 이름을 *SignalRChat*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-224">Name the project *SignalRChat*.</span></span> 

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="f292e-226">Razor Pages를 사용하는 프로젝트를 생성하려면 **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-226">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="f292e-227">**.NET Core**의 대상 프레임워크를 선택하고, **ASP.NET Core 2.2**를 선택하고, **확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-227">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-229">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="f292e-230">새 프로젝트 폴더를 만들 폴더에 대한 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-230">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="f292e-231">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-231">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-232">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-232">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="f292e-233">메뉴에서 **파일 > 새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-233">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="f292e-234">**.NET Core > 앱 > ASP.NET Core 웹앱**(**ASP.NET Core 웹앱(MVC) 선택 안 함**)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-234">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="f292e-235">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-235">Select **Next**.</span></span>  

* <span data-ttu-id="f292e-236">프로젝트 이름을 *SignalRChat*로 지정한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-236">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f292e-237">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="f292e-237">Add the SignalR client library</span></span>   

<span data-ttu-id="f292e-238">SignalR 서버 라이브러리는 `Microsoft.AspNetCore.App` 메타패키지에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-238">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="f292e-239">JavaScript 클라이언트 라이브러리는 프로젝트에 자동으로 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-239">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="f292e-240">본 자습서에서는 라이브러리 관리자(LibMan)를 사용하여 *unpkg*에서 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-240">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="f292e-241">unpkg는 Node.js의 패키지 관리자인 npm에서 찾은 모든 내용을 전달할 수 있는 콘텐츠 배달 네트워크(CDN, Content Delivery Network)입니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-241">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>    

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-242">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="f292e-243">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **클라이언트 쪽 라이브러리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-243">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="f292e-244">**클라이언트 쪽 라이브러리 추가** 대화 상자에서 **공급자**로 **unpkg**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-244">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="f292e-245">**라이브러리**에 `@aspnet/signalr@1`을 입력하고 미리 보기가 아닌 최신 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-245">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span> 

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 라이브러리 선택](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="f292e-247">**Choose specific files**(특정 파일 선택)를 선택하고 *dist/browser* 폴더를 확장한 후 *signalr.js* 및 *signalr.min.js*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-247">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="f292e-248">**대상 위치**를 *wwwroot/lib/signalr/* 로 설정하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-248">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 파일 및 대상 선택](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="f292e-250">그러면 LibMan이 *wwwroot/lib/signalr* 폴더를 생성한 다음 여기에 선택한 파일을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-250">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-251">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="f292e-252">통합 터미널에서 다음 명령을 실행하여 LibMan을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-252">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="f292e-253">다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-253">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="f292e-254">출력이 표시되기 전에 잠시 기다려야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-254">You might have to wait a few seconds before seeing output.</span></span>   

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="f292e-255">매개 변수는 다음 옵션을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-255">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="f292e-256">unpkg 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-256">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="f292e-257">파일을 *wwwroot/lib/signalr* 대상으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-257">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="f292e-258">지정된 파일만 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-258">Copy only the specified files.</span></span>  

  <span data-ttu-id="f292e-259">출력은 다음 예와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-259">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-260">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="f292e-261">**터미널**에서 다음 명령을 실행하여 LibMan을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-261">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="f292e-262">프로젝트 폴더로 이동합니다(*SignalRChat.csproj* 파일을 포함하는 폴더).</span><span class="sxs-lookup"><span data-stu-id="f292e-262">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="f292e-263">다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-263">Run the following command to get the SignalR client library by using LibMan.</span></span>  

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  <span data-ttu-id="f292e-264">매개 변수는 다음 옵션을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-264">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="f292e-265">unpkg 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-265">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="f292e-266">파일을 *wwwroot/lib/signalr* 대상으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-266">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="f292e-267">지정된 파일만 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-267">Copy only the specified files.</span></span>  

  <span data-ttu-id="f292e-268">출력은 다음 예와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-268">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="f292e-269">SignalR 허브 만들기</span><span class="sxs-lookup"><span data-stu-id="f292e-269">Create a SignalR hub</span></span> 

<span data-ttu-id="f292e-270">*허브*는 클라이언트-서버 통신을 처리하는 높은 수준의 파이프라인으로 제공되는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-270">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="f292e-271">SignalRChat 프로젝트 폴더에서 *Hubs* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-271">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="f292e-272">*Hubs* 폴더에 다음 코드를 사용하여 *ChatHub.cs* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-272">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="f292e-273">`ChatHub` 클래스는 SignalR `Hub` 클래스에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-273">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="f292e-274">`Hub` 클래스는 연결, 그룹 및 메시징을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-274">The `Hub` class manages connections, groups, and messaging.</span></span>    

  <span data-ttu-id="f292e-275">연결된 클라이언트에서 `SendMessage` 메서드를 호출하여 모든 클라이언트에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-275">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="f292e-276">메서드를 호출하는 JavaScript 클라이언트 코드는 자습서 뒷부분에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-276">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="f292e-277">SignalR 코드는 최대한의 확장성을 제공할 수 있도록 비동기적입니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-277">SignalR code is asynchronous to provide maximum scalability.</span></span>  

## <a name="configure-signalr"></a><span data-ttu-id="f292e-278">SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="f292e-278">Configure SignalR</span></span>    

<span data-ttu-id="f292e-279">SignalR 서버는 SignalR에 SignalR 요청을 전달하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-279">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>  

* <span data-ttu-id="f292e-280">다음 강조 표시된 코드를 *Startup.cs* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-280">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="f292e-281">이러한 변경 사항은 ASP.NET Core 종속성 주입 시스템 및 미들웨어 파이프라인에 SignalR을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-281">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>    

## <a name="add-signalr-client-code"></a><span data-ttu-id="f292e-282">SignalR 클라이언트 코드 추가</span><span class="sxs-lookup"><span data-stu-id="f292e-282">Add SignalR client code</span></span>  

* <span data-ttu-id="f292e-283">*Pages\Index.cshtml*의 콘텐츠를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-283">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="f292e-284">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="f292e-284">The preceding code:</span></span>   

  * <span data-ttu-id="f292e-285">이름 및 메시지 텍스트에 대한 텍스트 상자 및 전송 단추를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-285">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="f292e-286">SignalR 허브에서 받은 메시지를 표시하기 위해 `id="messagesList"`를 사용하여 목록을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-286">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span> 
  * <span data-ttu-id="f292e-287">SignalR에 대한 스크립트 참조 및 다음 단계에서 만드는 *chat.js* 애플리케이션 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-287">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>  

* <span data-ttu-id="f292e-288">*wwwroot/js* 폴더에서 다음 코드를 사용하여 *chat.js* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-288">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="f292e-289">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="f292e-289">The preceding code:</span></span>   

  * <span data-ttu-id="f292e-290">연결을 만들고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-290">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="f292e-291">허브에 메시지를 전송하는 처리기를 전송 단추에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-291">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="f292e-292">허브에서 메시지를 수신하고 목록에 추가하는 처리기를 연결 개체에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-292">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="f292e-293">앱 실행</span><span class="sxs-lookup"><span data-stu-id="f292e-293">Run the app</span></span>  

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f292e-294">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f292e-294">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="f292e-295">**CTRL+F5** 키를 눌러 디버깅 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-295">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f292e-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f292e-296">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="f292e-297">통합 터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-297">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli  
  dotnet run -p SignalRChat.csproj  
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f292e-298">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f292e-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="f292e-299">메뉴에서 **실행 > 디버깅하지 않고 시작**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-299">From the menu, select **Run > Start Without Debugging**.</span></span>  

--- 

* <span data-ttu-id="f292e-300">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-300">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>    

* <span data-ttu-id="f292e-301">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기 메시지** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-301">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="f292e-302">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-302">The name and message are displayed on both pages instantly.</span></span>   

  ![SignalR 샘플 앱](signalr/_static/2.x/signalr-get-started-finished.png)   

> [!TIP]    
> <span data-ttu-id="f292e-304">앱이 작동하지 않는 경우 브라우저 개발자 도구(F12)를 열고 콘솔로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-304">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="f292e-305">HTML 및 JavaScript 코드와 관련된 오류를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-305">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="f292e-306">예를 들어 지정되지 않은 다른 폴더에 *signalr.js*를 넣었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-306">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="f292e-307">이 경우 해당 파일에 대한 참조는 작동하지 않으며 콘솔에 404 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-307">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="f292e-308">![signalr.js 찾을 수 없음 오류](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="f292e-308">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="f292e-309">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f292e-309">Additional resources</span></span> 
* [<span data-ttu-id="f292e-310">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="f292e-310">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

## <a name="next-steps"></a><span data-ttu-id="f292e-311">다음 단계</span><span class="sxs-lookup"><span data-stu-id="f292e-311">Next steps</span></span>   

<span data-ttu-id="f292e-312">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-312">In this tutorial, you learned how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="f292e-313">웹앱 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-313">Create a web app project.</span></span>   
> * <span data-ttu-id="f292e-314">SignalR 클라이언트 라이브러리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-314">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="f292e-315">SignalR 허브를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-315">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="f292e-316">SignalR을 사용하도록 프로젝트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-316">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="f292e-317">허브를 사용하여 모든 클라이언트에서 연결된 모든 클라이언트에 메시지를 보내는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f292e-317">Add code that uses the hub to send messages from any client to all connected clients.</span></span>   
<span data-ttu-id="f292e-318">SignalR에 대해 자세히 알아보려면 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f292e-318">To learn more about SignalR, see the introduction:</span></span>  
> [!div class="nextstepaction"] 
> [<span data-ttu-id="f292e-319">ASP.NET Core SignalR 소개</span><span class="sxs-lookup"><span data-stu-id="f292e-319">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction) 
::: moniker-end

