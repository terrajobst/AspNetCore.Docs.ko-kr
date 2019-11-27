---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 7d495bddde3c01c743db9757204a5cf59d8b160b
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550320"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="a5eb6-103">ASP.NET Core Blazor 시작하기</span><span class="sxs-lookup"><span data-stu-id="a5eb6-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="a5eb6-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a5eb6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a5eb6-105">Blazor시작:</span><span class="sxs-lookup"><span data-stu-id="a5eb6-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="a5eb6-106">[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="a5eb6-107">명령 셸에서 다음 명령을 실행 하 여 [Blazor Weasembomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="a5eb6-108">[AspNetCoreBlazor입니다. 템플릿](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 있지만 Blazor Weasembmbveris는 미리 보기로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. <span data-ttu-id="a5eb6-109">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5eb6-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5eb6-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="a5eb6-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-111">1\.</span></span> <span data-ttu-id="a5eb6-112">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="a5eb6-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-113">2\.</span></span> <span data-ttu-id="a5eb6-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-114">Create a new project.</span></span>

   <span data-ttu-id="a5eb6-115">3\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-115">3\.</span></span> <span data-ttu-id="a5eb6-116">**Blazor 앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-116">Select **Blazor App**.</span></span> <span data-ttu-id="a5eb6-117">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-117">Select **Next**.</span></span>

   <span data-ttu-id="a5eb6-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-118">4\.</span></span> <span data-ttu-id="a5eb6-119">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a5eb6-120">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a5eb6-121">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-121">Select **Create**.</span></span>

   <span data-ttu-id="a5eb6-122">5\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-122">5\.</span></span> <span data-ttu-id="a5eb6-123">Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a5eb6-124">Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a5eb6-125">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-125">Select **Create**.</span></span> <span data-ttu-id="a5eb6-126">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a5eb6-127">6\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-127">6\.</span></span> <span data-ttu-id="a5eb6-128">**Ctrl**+**F5**를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a5eb6-129">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="a5eb6-130">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5eb6-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5eb6-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="a5eb6-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-132">1\.</span></span> <span data-ttu-id="a5eb6-133">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="a5eb6-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-134">2\.</span></span> <span data-ttu-id="a5eb6-135">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="a5eb6-136">3\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-136">3\.</span></span> <span data-ttu-id="a5eb6-137">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="a5eb6-138">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="a5eb6-139">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a5eb6-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-140">4\.</span></span> <span data-ttu-id="a5eb6-141">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="a5eb6-142">5\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-142">5\.</span></span> <span data-ttu-id="a5eb6-143">Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a5eb6-144">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-144">Select **Yes**.</span></span>

   <span data-ttu-id="a5eb6-145">6\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-145">6\.</span></span> <span data-ttu-id="a5eb6-146">Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="a5eb6-147">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="a5eb6-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-148">7\.</span></span> <span data-ttu-id="a5eb6-149">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a5eb6-150">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a5eb6-150">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="a5eb6-151">1 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-151">1\.</span></span> <span data-ttu-id="a5eb6-152">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-152">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="a5eb6-153">[업데이트 채널을 미리 보기로](/visualstudio/mac/install-preview)전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-153">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="a5eb6-154">2 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-154">2\.</span></span> <span data-ttu-id="a5eb6-155">**파일** > **새 솔루션** 을 선택 하거나 **새 프로젝트**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-155">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="a5eb6-156">3\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-156">3\.</span></span> <span data-ttu-id="a5eb6-157">사이드바에서 **.Net Core** > **앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-157">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="a5eb6-158">4 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-158">4\.</span></span> <span data-ttu-id="a5eb6-159">**Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-159">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="a5eb6-160">현재 Mac용 Visual Studio에서는 Blazor 서버 템플릿만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-160">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="a5eb6-161">Blazor Weasembomexperience의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-161">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="a5eb6-162">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-162">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="a5eb6-163">5\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-163">5\.</span></span> <span data-ttu-id="a5eb6-164">**대상 프레임 워크** 는 기본적으로 **.net core 3.0** (또는 3.1 Preview SDK가 설치 된 경우 **.net core 3.1** )로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-164">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="a5eb6-165">프레임 워크를 선택 하 고 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-165">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="a5eb6-166">6\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-166">6\.</span></span> <span data-ttu-id="a5eb6-167">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a5eb6-168">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-168">Select **Create**.</span></span>

   <span data-ttu-id="a5eb6-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-169">7\.</span></span> <span data-ttu-id="a5eb6-170">**디버깅 하지 않고** 실행 > 실행을 선택 하 여 *디버거 없이*앱 **을 실행 합니다** .</span><span class="sxs-lookup"><span data-stu-id="a5eb6-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a5eb6-171">**디버깅 시작** 을 사용 하 여 앱을 실행 하 고 *디버거를 사용*하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5eb6-172">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5eb6-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="a5eb6-173">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-173">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a5eb6-174">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-174">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a5eb6-175">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-175">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a5eb6-176">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-176">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="a5eb6-177">최신 [.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 릴리스를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-177">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="a5eb6-178">필요에 따라 [.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) 를 설치 하 고 명령 셸에서 다음 명령을 실행 하 여 [Blazor Weasembomtemplate](xref:blazor/hosting-models#blazor-webassembly) 템플릿을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-178">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. <span data-ttu-id="a5eb6-179">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-179">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5eb6-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5eb6-180">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="a5eb6-181">1 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-181">1\.</span></span> <span data-ttu-id="a5eb6-182">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 최신 [Visual Studio](https://visualstudio.com/vs/) 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-182">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="a5eb6-183">2 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-183">2\.</span></span> <span data-ttu-id="a5eb6-184">필요 Blazor에 따라 **ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-184">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="a5eb6-185">3\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-185">3\.</span></span> <span data-ttu-id="a5eb6-186">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-186">Create a new project.</span></span>

   <span data-ttu-id="a5eb6-187">4 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-187">4\.</span></span> <span data-ttu-id="a5eb6-188">**Blazor 앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-188">Select **Blazor App**.</span></span> <span data-ttu-id="a5eb6-189">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-189">Select **Next**.</span></span>

   <span data-ttu-id="a5eb6-190">5\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-190">5\.</span></span> <span data-ttu-id="a5eb6-191">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-191">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a5eb6-192">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-192">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a5eb6-193">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-193">Select **Create**.</span></span>

   <span data-ttu-id="a5eb6-194">6\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-194">6\.</span></span> <span data-ttu-id="a5eb6-195">Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-195">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a5eb6-196">Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-196">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a5eb6-197">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-197">Select **Create**.</span></span> <span data-ttu-id="a5eb6-198">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-198">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a5eb6-199">7 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-199">7\.</span></span> <span data-ttu-id="a5eb6-200">**F5** 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-200">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a5eb6-201">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-201">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="a5eb6-202">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-202">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5eb6-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5eb6-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="a5eb6-204">1 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-204">1\.</span></span> <span data-ttu-id="a5eb6-205">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-205">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="a5eb6-206">2 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-206">2\.</span></span> <span data-ttu-id="a5eb6-207">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-207">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="a5eb6-208">3\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-208">3\.</span></span> <span data-ttu-id="a5eb6-209">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-209">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="a5eb6-210">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-210">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="a5eb6-211">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-211">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a5eb6-212">4 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-212">4\.</span></span> <span data-ttu-id="a5eb6-213">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-213">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="a5eb6-214">5\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-214">5\.</span></span> <span data-ttu-id="a5eb6-215">Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-215">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a5eb6-216">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-216">Select **Yes**.</span></span>

   <span data-ttu-id="a5eb6-217">6\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-217">6\.</span></span> <span data-ttu-id="a5eb6-218">Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-218">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="a5eb6-219">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-219">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="a5eb6-220">7 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-220">7\.</span></span> <span data-ttu-id="a5eb6-221">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-221">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a5eb6-222">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a5eb6-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="a5eb6-223">1 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-223">1\.</span></span> <span data-ttu-id="a5eb6-224">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-224">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="a5eb6-225">[업데이트 채널을 미리 보기로](/visualstudio/mac/install-preview)전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-225">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="a5eb6-226">2 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-226">2\.</span></span> <span data-ttu-id="a5eb6-227">**파일** > **새 솔루션** 을 선택 하거나 **새 프로젝트**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-227">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="a5eb6-228">3\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-228">3\.</span></span> <span data-ttu-id="a5eb6-229">사이드바에서 **.Net Core** > **앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-229">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="a5eb6-230">4 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-230">4\.</span></span> <span data-ttu-id="a5eb6-231">**Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-231">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="a5eb6-232">현재 Mac용 Visual Studio에서는 Blazor 서버 템플릿만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-232">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="a5eb6-233">Blazor Weasembomexperience의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-233">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="a5eb6-234">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-234">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="a5eb6-235">5\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-235">5\.</span></span> <span data-ttu-id="a5eb6-236">**대상 프레임 워크** 는 기본적으로 **.net core 3.0** (또는 3.1 Preview SDK가 설치 된 경우 **.net core 3.1** )로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-236">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="a5eb6-237">프레임 워크를 선택 하 고 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-237">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="a5eb6-238">6\.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-238">6\.</span></span> <span data-ttu-id="a5eb6-239">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-239">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a5eb6-240">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-240">Select **Create**.</span></span>

   <span data-ttu-id="a5eb6-241">7 \.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-241">7\.</span></span> <span data-ttu-id="a5eb6-242">**디버깅 하지 않고** 실행 > 실행을 선택 하 여 *디버거 없이*앱 **을 실행 합니다** .</span><span class="sxs-lookup"><span data-stu-id="a5eb6-242">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a5eb6-243">**디버깅 시작** 을 사용 하 여 앱을 실행 하 고 *디버거를 사용*하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-243">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5eb6-244">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5eb6-244">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="a5eb6-245">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-245">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a5eb6-246">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-246">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a5eb6-247">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-247">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a5eb6-248">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-248">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="a5eb6-249">세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-249">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="a5eb6-250">홈</span><span class="sxs-lookup"><span data-stu-id="a5eb6-250">Home</span></span>
* <span data-ttu-id="a5eb6-251">카운터</span><span class="sxs-lookup"><span data-stu-id="a5eb6-251">Counter</span></span>
* <span data-ttu-id="a5eb6-252">데이터 페치</span><span class="sxs-lookup"><span data-stu-id="a5eb6-252">Fetch data</span></span>

<span data-ttu-id="a5eb6-253">Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-253">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a5eb6-254">웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만를 사용할 C#수 Blazor.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-254">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="a5eb6-255">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5eb6-255">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="a5eb6-256">맨 위에 있는 `@page` 지시문에 지정 된 대로 브라우저의 `/counter`에 대 한 요청은 `Counter` 구성 요소가 해당 내용을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-256">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="a5eb6-257">구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-257">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="a5eb6-258">**[클릭** ] 단추를 선택할 때마다:</span><span class="sxs-lookup"><span data-stu-id="a5eb6-258">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="a5eb6-259">`onclick` 이벤트가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-259">The `onclick` event is fired.</span></span>
* <span data-ttu-id="a5eb6-260">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-260">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="a5eb6-261">`currentCount` 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-261">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="a5eb6-262">구성 요소가 다시 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-262">The component is rendered again.</span></span>

<span data-ttu-id="a5eb6-263">런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-263">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="a5eb6-264">HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-264">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="a5eb6-265">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 앱의 홈페이지에 `Counter` 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-265">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="a5eb6-266">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5eb6-266">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="a5eb6-267">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-267">Run the app.</span></span> <span data-ttu-id="a5eb6-268">홈페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-268">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="a5eb6-269">구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-269">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="a5eb6-270">`Counter` 구성 요소에 매개 변수를 추가 하려면 구성 요소의 `@code` 블록을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-270">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="a5eb6-271">`[Parameter]` 특성을 사용 하 여 `IncrementAmount`에 대 한 public 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-271">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="a5eb6-272">`currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-272">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="a5eb6-273">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5eb6-273">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="a5eb6-274">특성을 사용 하 여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-274">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="a5eb6-275">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5eb6-275">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="a5eb6-276">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-276">Run the app.</span></span> <span data-ttu-id="a5eb6-277">`Index` 구성 요소에는 **클릭** 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-277">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="a5eb6-278">`/counter`의 `Counter` 구성 요소 ( *. razor*)는 1 씩 계속 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eb6-278">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a5eb6-279">다음 단계</span><span class="sxs-lookup"><span data-stu-id="a5eb6-279">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="a5eb6-280">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a5eb6-280">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
