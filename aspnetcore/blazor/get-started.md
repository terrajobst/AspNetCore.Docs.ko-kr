---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구로 Blazor 앱을 빌드하여 Blazor를 시작합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: bd33d874b3d6122f2ab820e9b147b0e62ba03a58
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648633"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="c10fb-103">ASP.NET Core Blazor 시작</span><span class="sxs-lookup"><span data-stu-id="c10fb-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="c10fb-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c10fb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c10fb-105">Blazor를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="c10fb-106">[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="c10fb-107">필요에 따라 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 템플릿을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="c10fb-108">[.NET Core 3.1 이상(미리 보기) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="c10fb-109">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-109">Run the following command in a command shell.</span></span> <span data-ttu-id="c10fb-110">[Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 포함되어 있으며 Blazor WebAssembly는 미리 보기로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
   ```

1. <span data-ttu-id="c10fb-111">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="c10fb-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="c10fb-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c10fb-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="c10fb-113">1\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-113">1\.</span></span> <span data-ttu-id="c10fb-114">**ASP.NET 및 웹 개발** 워크로드를 사용하여 [Visual Studio 2019 버전 16.4 이상](https://visualstudio.microsoft.com/vs/preview/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-114">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="c10fb-115">2\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-115">2\.</span></span> <span data-ttu-id="c10fb-116">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-116">Create a new project.</span></span>

   <span data-ttu-id="c10fb-117">3\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-117">3\.</span></span> <span data-ttu-id="c10fb-118">**Blazor 앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-118">Select **Blazor App**.</span></span> <span data-ttu-id="c10fb-119">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-119">Select **Next**.</span></span>

   <span data-ttu-id="c10fb-120">4\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-120">4\.</span></span> <span data-ttu-id="c10fb-121">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="c10fb-122">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="c10fb-123">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-123">Select **Create**.</span></span>

   <span data-ttu-id="c10fb-124">5\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-124">5\.</span></span> <span data-ttu-id="c10fb-125">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="c10fb-126">Blazor 서버 환경의 경우 **Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="c10fb-127">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-127">Select **Create**.</span></span> <span data-ttu-id="c10fb-128">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c10fb-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="c10fb-129">6\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-129">6\.</span></span> <span data-ttu-id="c10fb-130">**Ctrl**+**F5**를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c10fb-131">ASP.NET Core Blazor의 이전 미리 보기 릴리스(Preview 6 또는 이전 버전)용 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="c10fb-132">이제 명령 셸에서 Blazor 템플릿을 설치하기만 하면 Visual Studio에 템플릿을 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="c10fb-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c10fb-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="c10fb-134">1\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-134">1\.</span></span> <span data-ttu-id="c10fb-135">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="c10fb-136">2\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-136">2\.</span></span> <span data-ttu-id="c10fb-137">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="c10fb-138">3\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-138">3\.</span></span> <span data-ttu-id="c10fb-139">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="c10fb-140">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="c10fb-141">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c10fb-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="c10fb-142">4\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-142">4\.</span></span> <span data-ttu-id="c10fb-143">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="c10fb-144">5\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-144">5\.</span></span> <span data-ttu-id="c10fb-145">Blazor 서버 프로젝트의 경우 IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="c10fb-146">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-146">Select **Yes**.</span></span>

   <span data-ttu-id="c10fb-147">6\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-147">6\.</span></span> <span data-ttu-id="c10fb-148">Blazor 서버 앱을 사용하는 경우 Visual Studio Code 디버거를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="c10fb-149">Blazor WebAssembly 앱을 사용하는 경우 앱의 프로젝트 폴더에서 `dotnet run`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="c10fb-150">7\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-150">7\.</span></span> <span data-ttu-id="c10fb-151">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c10fb-152">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c10fb-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="c10fb-153">1\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-153">1\.</span></span> <span data-ttu-id="c10fb-154">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="c10fb-155">2\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-155">2\.</span></span> <span data-ttu-id="c10fb-156">**파일** > **새 솔루션**을 선택하거나 **새 프로젝트**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="c10fb-157">3\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-157">3\.</span></span> <span data-ttu-id="c10fb-158">사이드바에서 **.NET Core** > **앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="c10fb-159">4\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-159">4\.</span></span> <span data-ttu-id="c10fb-160">**Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="c10fb-161">지금은 Mac용 Visual Studio에서 Blazor 서버 템플릿만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="c10fb-162">Blazor WebAssembly 환경의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="c10fb-163">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c10fb-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="c10fb-164">5\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-164">5\.</span></span> <span data-ttu-id="c10fb-165">**대상 프레임워크**를 **.NET Core 3.1**로 설정하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="c10fb-166">6\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-166">6\.</span></span> <span data-ttu-id="c10fb-167">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="c10fb-168">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-168">Select **Create**.</span></span>

   <span data-ttu-id="c10fb-169">7\.</span><span class="sxs-lookup"><span data-stu-id="c10fb-169">7\.</span></span> <span data-ttu-id="c10fb-170">**실행** > **디버깅하지 않고 실행**을 선택하여 ‘디버거 없이’ 앱을 실행합니다. </span><span class="sxs-lookup"><span data-stu-id="c10fb-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="c10fb-171">‘디버거로’ 앱을 실행하려면 **디버깅 시작**을 사용하여 앱을 실행합니다. </span><span class="sxs-lookup"><span data-stu-id="c10fb-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="c10fb-172">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="c10fb-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c10fb-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="c10fb-174">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="c10fb-175">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="c10fb-176">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c10fb-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="c10fb-177">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="c10fb-178">사이드바의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="c10fb-179">Home</span><span class="sxs-lookup"><span data-stu-id="c10fb-179">Home</span></span>
* <span data-ttu-id="c10fb-180">카운터</span><span class="sxs-lookup"><span data-stu-id="c10fb-180">Counter</span></span>
* <span data-ttu-id="c10fb-181">데이터 가져오기</span><span class="sxs-lookup"><span data-stu-id="c10fb-181">Fetch data</span></span>

<span data-ttu-id="c10fb-182">Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="c10fb-183">웹 페이지에서 카운터를 증가하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor에서는 C#을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="c10fb-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="c10fb-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="c10fb-185">맨 위에 있는 `@page` 지시문에 지정된 대로 브라우저에서 `/counter`를 요청하면 `Counter` 구성 요소가 해당 콘텐츠를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="c10fb-186">구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용할 수 있는 렌더링 트리의 메모리 내 표시로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="c10fb-187">**Click me** 단추를 선택할 때마다 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="c10fb-188">`onclick` 이벤트가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="c10fb-189">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="c10fb-190">`currentCount`가 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="c10fb-191">구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-191">The component is rendered again.</span></span>

<span data-ttu-id="c10fb-192">런타임은 이전 콘텐츠와 새 콘텐츠를 비교하고 변경된 콘텐츠만 DOM(문서 개체 모델)에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="c10fb-193">HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="c10fb-194">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가하여 앱의 홈페이지에 `Counter` 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="c10fb-195">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="c10fb-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="c10fb-196">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-196">Run the app.</span></span> <span data-ttu-id="c10fb-197">홈페이지에는 `Counter` 구성 요소가 제공하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="c10fb-198">구성 요소 매개 변수는 자식 구성 요소에 속성을 설정하게 해주는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="c10fb-199">`Counter` 구성 요소에 매개 변수를 추가하려면 구성 요소의 `@code` 블록을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="c10fb-200">`[Parameter]` 특성을 사용하여 `IncrementAmount`의 공용 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="c10fb-201">`currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="c10fb-202">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="c10fb-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="c10fb-203">특성을 사용하여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="c10fb-204">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="c10fb-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="c10fb-205">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-205">Run the app.</span></span> <span data-ttu-id="c10fb-206">`Index` 구성 요소에는 **Click me** 단추를 선택할 때마다 10씩 증가하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="c10fb-207">`/counter`의 `Counter` 구성 요소(*Counter.razor*)는 계속 1씩 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="c10fb-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c10fb-208">다음 단계</span><span class="sxs-lookup"><span data-stu-id="c10fb-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="c10fb-209">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c10fb-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
