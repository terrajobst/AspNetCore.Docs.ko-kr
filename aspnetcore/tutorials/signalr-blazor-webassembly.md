---
title: Blazor WebAssembly를 활용해 ASP.NET Core SignalR 사용
author: guardrex
description: Blazor WebAssembly를 활용해 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: d3605c0823e9ec3ce34fb781da66a7470aa00622
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034146"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="bd1d1-103">Blazor WebAssembly를 활용해 ASP.NET Core SignalR 사용</span><span class="sxs-lookup"><span data-stu-id="bd1d1-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="bd1d1-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd1d1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="bd1d1-105">이 자습서에서는 Blazor WebAssembly를 활용하여 SignalR을 사용하는 실시간 앱을 빌드하는 방법에 대한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="bd1d1-106">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd1d1-107">Blazor WebAssembly 호스팅 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="bd1d1-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="bd1d1-108">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="bd1d1-109">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="bd1d1-110">SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="bd1d1-111">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-111">Add Razor component code for chat</span></span>

<span data-ttu-id="bd1d1-112">이 모든 과정을 마치면 채팅 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="bd1d1-113">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd1d1-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bd1d1-114">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="bd1d1-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd1d1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd1d1-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd1d1-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd1d1-117">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd1d1-118">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd1d1-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="bd1d1-119">호스팅된 Blazor WebAssembly 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="bd1d1-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="bd1d1-120">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 템플릿을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-120">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="bd1d1-121">[Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 포함되어 있으며 Blazor WebAssembly는 미리 보기로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-121">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="bd1d1-122">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
```

<span data-ttu-id="bd1d1-123">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd1d1-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bd1d1-125">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-125">Create a new project.</span></span>

1. <span data-ttu-id="bd1d1-126">**Blazor 앱**을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="bd1d1-127">**프로젝트 이름** 필드에 "BlazorSignalRApp"을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="bd1d1-128">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="bd1d1-129">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-129">Select **Create**.</span></span>

1. <span data-ttu-id="bd1d1-130">**Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="bd1d1-131">**고급**에서 **ASP.NET Core 호스팅** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="bd1d1-132">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="bd1d1-133">Visual Studio를 새 버전으로 업그레이드했거나 새 버전을 설치했는데 Blazor WebAssembly 템플릿이 VS UI에 표시되지 않는 경우, 앞서 살펴본 `dotnet new` 명령을 사용하여 템플릿을 다시 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd1d1-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd1d1-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="bd1d1-135">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="bd1d1-136">Visual Studio Code에서 앱의 프로젝트 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="bd1d1-137">앱을 빌드하고 디버그할 자산을 추가하기 위한 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="bd1d1-138">Visual Studio Code는 생성된 *launch.json* 및 *tasks.json* 파일로 *.vscode* 폴더를 자동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd1d1-139">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bd1d1-140">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="bd1d1-141">Mac용 Visual Studio에서 프로젝트 폴더로 이동하고 프로젝트의 솔루션 파일( *.sln*)을 열어 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd1d1-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd1d1-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bd1d1-143">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="bd1d1-144">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-144">Add the SignalR client library</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd1d1-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="bd1d1-146">**솔루션 탐색기**에서 **BlazorSignalRApp.Client** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="bd1d1-147">**NuGet 패키지 관리** 대화 상자에서 **패키지 원본**이 *nuget.org*로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="bd1d1-148">**찾아보기**를 선택한 상태에서 검색 상자에 "Microsoft.AspNetCore.SignalR.Client"를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="bd1d1-149">검색 결과에서 `Microsoft.AspNetCore.SignalR.Client` 패키지를 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="bd1d1-150">**변경 내용 미리 보기** 대화 상자가 표시되면 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="bd1d1-151">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd1d1-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd1d1-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="bd1d1-153">**통합 터미널**(도구 모음에서 **보기** > **터미널**)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd1d1-154">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bd1d1-155">**솔루션** 사이드바에서 **BlazorSignalRApp.Client** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="bd1d1-156">**NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 *nuget.org*로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="bd1d1-157">**찾아보기**를 선택한 상태에서 검색 상자에 "Microsoft.AspNetCore.SignalR.Client"를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="bd1d1-158">검색 결과에서 `Microsoft.AspNetCore.SignalR.Client` 패키지 옆의 확인란을 선택하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="bd1d1-159">**라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd1d1-160">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd1d1-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bd1d1-161">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="bd1d1-162">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-162">Add a SignalR hub</span></span>

<span data-ttu-id="bd1d1-163">**BlazorSignalRApp.Server** 프로젝트에서 *Hubs*(복수형) 폴더를 만들고 다음 `ChatHub` 클래스(*Hubs/ChatHub.cs*)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="bd1d1-164">SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="bd1d1-165">**BlazorSignalRApp.Server** 프로젝트에서 *Startup.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="bd1d1-166">`ChatHub` 클래스에 대한 네임스페이스를 파일의 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="bd1d1-167">`Startup.ConfigureServices`에 SignalR 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="bd1d1-168">기본 컨트롤러 경로와 클라이언트 쪽 대체에 대한 엔드포인트 간의 `Startup.Configure`에서 허브에 대한 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="bd1d1-169">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="bd1d1-170">**BlazorSignalRApp.Client** 프로젝트에서 *Pages/Index.razor* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="bd1d1-171">태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="bd1d1-172">앱 실행</span><span class="sxs-lookup"><span data-stu-id="bd1d1-172">Run the app</span></span>

1. <span data-ttu-id="bd1d1-173">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd1d1-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bd1d1-175">**솔루션 탐색기**에서 **BlazorSignalRApp.Server** 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="bd1d1-176">**CTRL+F5** 키를 눌러 디버깅 없이 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="bd1d1-177">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd1d1-178">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="bd1d1-179">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd1d1-181">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd1d1-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bd1d1-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd1d1-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="bd1d1-183">도구 모음에서 **디버그** > **디버그깅하지 않고 실행**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="bd1d1-184">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd1d1-185">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="bd1d1-186">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-186">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd1d1-188">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd1d1-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bd1d1-189">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd1d1-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bd1d1-190">**솔루션** 사이드바에서 **BlazorSignalRApp.Server** 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="bd1d1-191">메뉴에서 **실행** > **디버깅하지 않고 시작**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="bd1d1-192">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd1d1-193">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="bd1d1-194">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-194">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd1d1-196">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd1d1-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd1d1-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd1d1-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="bd1d1-198">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="bd1d1-199">주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd1d1-200">브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="bd1d1-201">이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-201">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd1d1-203">인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd1d1-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="bd1d1-204">다음 단계</span><span class="sxs-lookup"><span data-stu-id="bd1d1-204">Next steps</span></span>

<span data-ttu-id="bd1d1-205">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd1d1-206">Blazor WebAssembly 호스팅 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="bd1d1-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="bd1d1-207">SignalR 클라이언트 라이브러리 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="bd1d1-208">SignalR 허브 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="bd1d1-209">SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="bd1d1-210">채팅을 위한 Razor 구성 요소 코드 추가</span><span class="sxs-lookup"><span data-stu-id="bd1d1-210">Add Razor component code for chat</span></span>

<span data-ttu-id="bd1d1-211">Blazor 앱 빌드에 대한 자세한 내용은 Blazor 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bd1d1-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="bd1d1-212">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bd1d1-212">Additional resources</span></span>

* <xref:signalr/introduction>
