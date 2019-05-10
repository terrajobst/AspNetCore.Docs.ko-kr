---
title: ASP.NET Core SignalR로 시작
author: bradygaster
description: 이 자습서에서는 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: 9a77460cfd8201ca357aad3415725d4b9a30b187
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64885048"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="a2727-103">자습서: ASP.NET Core SignalR 시작</span><span class="sxs-lookup"><span data-stu-id="a2727-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="a2727-104">본 자습서에서는 SignalR을 이용해서 실시간 앱을 구현하기 위한 기본 사항을 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="a2727-105">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a2727-106">웹 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-106">Create a web project.</span></span>
> * <span data-ttu-id="a2727-107">SignalR 클라이언트 라이브러리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="a2727-108">SignalR 허브를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="a2727-109">SignalR을 사용하도록 프로젝트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="a2727-110">모든 클라이언트에서 연결된 모든 클라이언트로 메시지를 보내는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="a2727-111">이 모든 과정을 마치면 동작하는 채팅 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-111">At the end, you'll have a working chat app:</span></span>

![SignalR 샘플 앱](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="a2727-113">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2727-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

[!INCLUDE [|Prerequisites](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="a2727-114">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="a2727-114">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a2727-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2727-115">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a2727-116">메뉴에서 **파일 > 새 프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-116">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="a2727-117">**새 프로젝트** 대화 상자에서 **설치됨 &gt; Visual C# &gt; 웹 &gt; ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-117">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a2727-118">프로젝트의 이름을 *SignalRChat*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-118">Name the project *SignalRChat*.</span></span>

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="a2727-120">Razor Pages를 사용하는 프로젝트를 생성하려면 **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-120">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="a2727-121">**.NET Core**의 대상 프레임워크를 선택하고, **ASP.NET Core 2.2**를 선택하고, **확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-121">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Visual Studio의 새 프로젝트 대화 상자](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a2727-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a2727-123">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a2727-124">새 프로젝트 폴더를 만들 폴더에 대한 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-124">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="a2727-125">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-125">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a2727-126">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a2727-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a2727-127">메뉴에서 **파일 > 새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-127">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="a2727-128">**.NET Core > 앱 > ASP.NET Core 웹앱**(**ASP.NET Core 웹앱(MVC) 선택 안 함**)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-128">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="a2727-129">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-129">Select **Next**.</span></span>

* <span data-ttu-id="a2727-130">프로젝트 이름을 *SignalRChat*로 지정한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-130">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="a2727-131">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="a2727-131">Add the SignalR client library</span></span>

<span data-ttu-id="a2727-132">SignalR 서버 라이브러리는 `Microsoft.AspNetCore.App` 메타패키지에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-132">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="a2727-133">JavaScript 클라이언트 라이브러리는 프로젝트에 자동으로 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-133">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="a2727-134">본 자습서에서는 라이브러리 관리자(LibMan)를 사용하여 *unpkg*에서 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-134">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="a2727-135">unpkg는 Node.js의 패키지 관리자인 npm에서 찾은 모든 내용을 전달할 수 있는 콘텐츠 배달 네트워크(CDN, Content Delivery Network)입니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-135">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a2727-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2727-136">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a2727-137">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **클라이언트 쪽 라이브러리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-137">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="a2727-138">**클라이언트 쪽 라이브러리 추가** 대화 상자에서 **공급자**로 **unpkg**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-138">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="a2727-139">**라이브러리**에 `@aspnet/signalr@1`을 입력하고 미리 보기가 아닌 최신 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-139">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 라이브러리 선택](signalr/_static/libman1.png)

* <span data-ttu-id="a2727-141">**Choose specific files**(특정 파일 선택)를 선택하고 *dist/browser* 폴더를 확장한 후 *signalr.js* 및 *signalr.min.js*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-141">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="a2727-142">**대상 위치**를 *wwwroot/lib/signalr/* 로 설정하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-142">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![클라이언트 쪽 라이브러리 추가 대화 상자 - 파일 및 대상 선택](signalr/_static/libman2.png)

  <span data-ttu-id="a2727-144">그러면 LibMan이 *wwwroot/lib/signalr* 폴더를 생성한 다음 여기에 선택한 파일을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-144">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a2727-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a2727-145">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a2727-146">통합 터미널에서 다음 명령을 실행하여 LibMan을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-146">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a2727-147">다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-147">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="a2727-148">출력이 표시되기 전에 잠시 기다려야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-148">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a2727-149">매개 변수는 다음 옵션을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-149">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a2727-150">unpkg 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-150">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a2727-151">파일을 *wwwroot/lib/signalr* 대상으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-151">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="a2727-152">지정된 파일만 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-152">Copy only the specified files.</span></span>

  <span data-ttu-id="a2727-153">출력은 다음 예와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-153">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a2727-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a2727-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a2727-155">**터미널**에서 다음 명령을 실행하여 LibMan을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-155">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a2727-156">프로젝트 폴더로 이동합니다(*SignalRChat.csproj* 파일을 포함하는 폴더).</span><span class="sxs-lookup"><span data-stu-id="a2727-156">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="a2727-157">다음 명령을 실행하고 LibMan을 사용하여 SignalR 클라이언트 라이브러리를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-157">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a2727-158">매개 변수는 다음 옵션을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-158">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a2727-159">unpkg 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-159">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a2727-160">파일을 *wwwroot/lib/signalr* 대상으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-160">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="a2727-161">지정된 파일만 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-161">Copy only the specified files.</span></span>

  <span data-ttu-id="a2727-162">출력은 다음 예와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-162">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="a2727-163">SignalR 허브 만들기</span><span class="sxs-lookup"><span data-stu-id="a2727-163">Create a SignalR hub</span></span>

<span data-ttu-id="a2727-164">*허브*는 클라이언트-서버 통신을 처리하는 높은 수준의 파이프라인으로 제공되는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-164">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="a2727-165">SignalRChat 프로젝트 폴더에서 *Hubs* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-165">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="a2727-166">*Hubs* 폴더에 다음 코드를 사용하여 *ChatHub.cs* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-166">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="a2727-167">`ChatHub` 클래스는 SignalR `Hub` 클래스에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-167">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="a2727-168">`Hub` 클래스는 연결, 그룹 및 메시징을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-168">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="a2727-169">연결된 클라이언트에서 `SendMessage` 메서드를 호출하여 모든 클라이언트에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-169">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="a2727-170">메서드를 호출하는 JavaScript 클라이언트 코드는 자습서 뒷부분에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-170">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="a2727-171">SignalR 코드는 최대한의 확장성을 제공할 수 있도록 비동기적입니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-171">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="a2727-172">SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="a2727-172">Configure SignalR</span></span>

<span data-ttu-id="a2727-173">SignalR 서버는 SignalR에 SignalR 요청을 전달하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-173">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="a2727-174">다음 강조 표시된 코드를 *Startup.cs* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-174">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="a2727-175">이러한 변경 사항은 ASP.NET Core 종속성 주입 시스템 및 미들웨어 파이프라인에 SignalR을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-175">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="a2727-176">SignalR 클라이언트 코드 추가</span><span class="sxs-lookup"><span data-stu-id="a2727-176">Add SignalR client code</span></span>

* <span data-ttu-id="a2727-177">*Pages\Index.cshtml*의 콘텐츠를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-177">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="a2727-178">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="a2727-178">The preceding code:</span></span>

  * <span data-ttu-id="a2727-179">이름 및 메시지 텍스트에 대한 텍스트 상자 및 전송 단추를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-179">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="a2727-180">SignalR 허브에서 받은 메시지를 표시하기 위해 `id="messagesList"`를 사용하여 목록을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-180">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="a2727-181">SignalR에 대한 스크립트 참조 및 다음 단계에서 만드는 *chat.js* 애플리케이션 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-181">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="a2727-182">*wwwroot/js* 폴더에서 다음 코드를 사용하여 *chat.js* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-182">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="a2727-183">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="a2727-183">The preceding code:</span></span>

  * <span data-ttu-id="a2727-184">연결을 만들고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-184">Creates and starts a connection.</span></span>
  * <span data-ttu-id="a2727-185">허브에 메시지를 전송하는 처리기를 전송 단추에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-185">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="a2727-186">허브에서 메시지를 수신하고 목록에 추가하는 처리기를 연결 개체에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-186">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="a2727-187">앱 실행</span><span class="sxs-lookup"><span data-stu-id="a2727-187">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a2727-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2727-188">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a2727-189">**CTRL+F5** 키를 눌러 디버깅 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-189">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a2727-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a2727-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a2727-191">통합 터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-191">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a2727-192">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a2727-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a2727-193">메뉴에서 **실행 > 디버깅하지 않고 시작**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-193">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="a2727-194">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-194">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="a2727-195">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기 메시지** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-195">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="a2727-196">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-196">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR 샘플 앱](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="a2727-198">앱이 작동하지 않는 경우 브라우저 개발자 도구(F12)를 열고 콘솔로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-198">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="a2727-199">HTML 및 JavaScript 코드와 관련된 오류를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-199">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="a2727-200">예를 들어 지정되지 않은 다른 폴더에 *signalr.js*를 넣었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-200">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="a2727-201">이 경우 해당 파일에 대한 참조는 작동하지 않으며 콘솔에 404 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-201">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="a2727-202">![signalr.js 찾을 수 없음 오류](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="a2727-202">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="a2727-203">다음 단계</span><span class="sxs-lookup"><span data-stu-id="a2727-203">Next steps</span></span>

<span data-ttu-id="a2727-204">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-204">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a2727-205">웹앱 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-205">Create a web app project.</span></span>
> * <span data-ttu-id="a2727-206">SignalR 클라이언트 라이브러리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-206">Add the SignalR client library.</span></span>
> * <span data-ttu-id="a2727-207">SignalR 허브를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-207">Create a SignalR hub.</span></span>
> * <span data-ttu-id="a2727-208">SignalR을 사용하도록 프로젝트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-208">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="a2727-209">허브를 사용하여 모든 클라이언트에서 연결된 모든 클라이언트에 메시지를 보내는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a2727-209">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="a2727-210">SignalR에 대해 자세히 알아보려면 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a2727-210">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a2727-211">ASP.NET Core SignalR 소개</span><span class="sxs-lookup"><span data-stu-id="a2727-211">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
