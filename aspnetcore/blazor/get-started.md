---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구로 Blazor 앱을 빌드하여 Blazor를 시작합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083247"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="c6dfd-103">ASP.NET Core Blazor 시작</span><span class="sxs-lookup"><span data-stu-id="c6dfd-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="c6dfd-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c6dfd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c6dfd-105">Blazor를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="c6dfd-106">[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="c6dfd-107">필요에 따라 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 템플릿을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="c6dfd-108">[.NET Core 3.1.102 이상(미리 보기) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-108">Install the [.NET Core 3.1.102 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="c6dfd-109">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-109">Run the following command in a command shell.</span></span> <span data-ttu-id="c6dfd-110">[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) 패키지에는 미리 보기 버전이 포함되어 있으며 Blazor WebAssembly는 미리 보기로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-110">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > <span data-ttu-id="c6dfd-111">3\.2 미리 보기 2 Blazor WebAssembly 템플릿을 사용하려면 .NET Core SDK 버전 3.1.102 이상이 **필요**합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-111">.NET Core SDK version 3.1.102 or later is **required** to use the 3.2 Preview 2 Blazor WebAssembly template.</span></span> <span data-ttu-id="c6dfd-112">명령 셸에서 `dotnet --version`을 실행하여 설치된 .NET Core SDK 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-112">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="c6dfd-113">선택한 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-113">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="c6dfd-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6dfd-114">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="c6dfd-115">1\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-115">1\.</span></span> <span data-ttu-id="c6dfd-116">**ASP.NET 및 웹 개발** 워크로드를 사용하여 [Visual Studio 2019 버전 16.4 이상](https://visualstudio.microsoft.com/vs/preview/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-116">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="c6dfd-117">2\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-117">2\.</span></span> <span data-ttu-id="c6dfd-118">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-118">Create a new project.</span></span>

   <span data-ttu-id="c6dfd-119">3\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-119">3\.</span></span> <span data-ttu-id="c6dfd-120">**Blazor 앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-120">Select **Blazor App**.</span></span> <span data-ttu-id="c6dfd-121">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-121">Select **Next**.</span></span>

   <span data-ttu-id="c6dfd-122">4\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-122">4\.</span></span> <span data-ttu-id="c6dfd-123">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-123">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="c6dfd-124">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="c6dfd-125">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-125">Select **Create**.</span></span>

   <span data-ttu-id="c6dfd-126">5\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-126">5\.</span></span> <span data-ttu-id="c6dfd-127">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-127">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="c6dfd-128">Blazor 서버 환경의 경우 **Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-128">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="c6dfd-129">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-129">Select **Create**.</span></span> <span data-ttu-id="c6dfd-130">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c6dfd-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="c6dfd-131">Blazor WebAssembly 템플릿이 없으면 이전 단계로 돌아가서 템플릿을 다시 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-131">If the Blazor WebAssembly template isn't present, return to the previous step and reinstall the template.</span></span>

   <span data-ttu-id="c6dfd-132">6\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-132">6\.</span></span> <span data-ttu-id="c6dfd-133">**Ctrl**+**F5**를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-133">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="c6dfd-134">ASP.NET Core Blazor의 이전 미리 보기 릴리스(Preview 6 또는 이전 버전)용 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-134">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="c6dfd-135">이제 명령 셸에서 Blazor 템플릿을 설치하기만 하면 Visual Studio에 템플릿을 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-135">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="c6dfd-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6dfd-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="c6dfd-137">1\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-137">1\.</span></span> <span data-ttu-id="c6dfd-138">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-138">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="c6dfd-139">2\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-139">2\.</span></span> <span data-ttu-id="c6dfd-140">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-140">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

   <span data-ttu-id="c6dfd-141">3\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-141">3\.</span></span> <span data-ttu-id="c6dfd-142">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-142">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="c6dfd-143">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-143">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="c6dfd-144">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c6dfd-144">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="c6dfd-145">4\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-145">4\.</span></span> <span data-ttu-id="c6dfd-146">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-146">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="c6dfd-147">5\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-147">5\.</span></span> <span data-ttu-id="c6dfd-148">Blazor 서버 프로젝트의 경우 IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-148">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="c6dfd-149">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-149">Select **Yes**.</span></span>

   <span data-ttu-id="c6dfd-150">6\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-150">6\.</span></span> <span data-ttu-id="c6dfd-151">Blazor 서버 앱을 사용하는 경우 Visual Studio Code 디버거를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-151">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="c6dfd-152">Blazor WebAssembly 앱을 사용하는 경우 앱의 프로젝트 폴더에서 `dotnet run`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-152">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="c6dfd-153">7\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-153">7\.</span></span> <span data-ttu-id="c6dfd-154">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6dfd-155">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6dfd-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="c6dfd-156">1\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-156">1\.</span></span> <span data-ttu-id="c6dfd-157">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-157">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="c6dfd-158">2\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-158">2\.</span></span> <span data-ttu-id="c6dfd-159">**파일** > **새 솔루션**을 선택하거나 **새 프로젝트**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-159">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="c6dfd-160">3\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-160">3\.</span></span> <span data-ttu-id="c6dfd-161">사이드바에서 **.NET Core** > **앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-161">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="c6dfd-162">4\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-162">4\.</span></span> <span data-ttu-id="c6dfd-163">**Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-163">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="c6dfd-164">지금은 Mac용 Visual Studio에서 Blazor 서버 템플릿만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-164">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="c6dfd-165">Blazor WebAssembly 환경의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-165">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="c6dfd-166">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c6dfd-166">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="c6dfd-167">5\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-167">5\.</span></span> <span data-ttu-id="c6dfd-168">**대상 프레임워크**를 **.NET Core 3.1**로 설정하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-168">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="c6dfd-169">6\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-169">6\.</span></span> <span data-ttu-id="c6dfd-170">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-170">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="c6dfd-171">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-171">Select **Create**.</span></span>

   <span data-ttu-id="c6dfd-172">7\.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-172">7\.</span></span> <span data-ttu-id="c6dfd-173">**실행** > **디버깅하지 않고 실행**을 선택하여 ‘디버거 없이’ 앱을 실행합니다. </span><span class="sxs-lookup"><span data-stu-id="c6dfd-173">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="c6dfd-174">‘디버거로’ 앱을 실행하려면 **디버깅 시작**을 사용하여 앱을 실행합니다. </span><span class="sxs-lookup"><span data-stu-id="c6dfd-174">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="c6dfd-175">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-175">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="c6dfd-176">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c6dfd-176">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="c6dfd-177">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-177">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="c6dfd-178">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-178">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="c6dfd-179">두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="c6dfd-179">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="c6dfd-180">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-180">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="c6dfd-181">사이드바의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-181">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="c6dfd-182">Home</span><span class="sxs-lookup"><span data-stu-id="c6dfd-182">Home</span></span>
* <span data-ttu-id="c6dfd-183">카운터</span><span class="sxs-lookup"><span data-stu-id="c6dfd-183">Counter</span></span>
* <span data-ttu-id="c6dfd-184">데이터 가져오기</span><span class="sxs-lookup"><span data-stu-id="c6dfd-184">Fetch data</span></span>

<span data-ttu-id="c6dfd-185">Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-185">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="c6dfd-186">웹 페이지에서 카운터를 증가하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor에서는 C#을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-186">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="c6dfd-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="c6dfd-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="c6dfd-188">맨 위에 있는 `@page` 지시문에 지정된 대로 브라우저에서 `/counter`를 요청하면 `Counter` 구성 요소가 해당 콘텐츠를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-188">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="c6dfd-189">구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용할 수 있는 렌더링 트리의 메모리 내 표시로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-189">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="c6dfd-190">**Click me** 단추를 선택할 때마다 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-190">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="c6dfd-191">`onclick` 이벤트가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-191">The `onclick` event is fired.</span></span>
* <span data-ttu-id="c6dfd-192">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-192">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="c6dfd-193">`currentCount`가 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-193">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="c6dfd-194">구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-194">The component is rendered again.</span></span>

<span data-ttu-id="c6dfd-195">런타임은 이전 콘텐츠와 새 콘텐츠를 비교하고 변경된 콘텐츠만 DOM(문서 개체 모델)에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-195">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="c6dfd-196">HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-196">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="c6dfd-197">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가하여 앱의 홈페이지에 `Counter` 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-197">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="c6dfd-198">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="c6dfd-198">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="c6dfd-199">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-199">Run the app.</span></span> <span data-ttu-id="c6dfd-200">홈페이지에는 `Counter` 구성 요소가 제공하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-200">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="c6dfd-201">구성 요소 매개 변수는 자식 구성 요소에 속성을 설정하게 해주는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-201">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="c6dfd-202">`Counter` 구성 요소에 매개 변수를 추가하려면 구성 요소의 `@code` 블록을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-202">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="c6dfd-203">`[Parameter]` 특성을 사용하여 `IncrementAmount`의 공용 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-203">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="c6dfd-204">`currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-204">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="c6dfd-205">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="c6dfd-205">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="c6dfd-206">특성을 사용하여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-206">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="c6dfd-207">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="c6dfd-207">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="c6dfd-208">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-208">Run the app.</span></span> <span data-ttu-id="c6dfd-209">`Index` 구성 요소에는 **Click me** 단추를 선택할 때마다 10씩 증가하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-209">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="c6dfd-210">`/counter`의 `Counter` 구성 요소(*Counter.razor*)는 계속 1씩 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="c6dfd-210">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c6dfd-211">다음 단계</span><span class="sxs-lookup"><span data-stu-id="c6dfd-211">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="c6dfd-212">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c6dfd-212">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
