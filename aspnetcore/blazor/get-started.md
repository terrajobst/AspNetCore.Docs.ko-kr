---
title: ASP.NET Core Blazor 시작 하기
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 하세요.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/get-started
ms.openlocfilehash: fc368be5eb2e5d8f7c80071dc86a02ae986a685f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391050"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="93fde-103">ASP.NET Core Blazor 시작 하기</span><span class="sxs-lookup"><span data-stu-id="93fde-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="93fde-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="93fde-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="93fde-105">Blazor 시작 하기:</span><span class="sxs-lookup"><span data-stu-id="93fde-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="93fde-106">[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="93fde-107">명령 셸에서 다음 명령을 실행 하 여 [Blazor Weasembomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="93fde-108">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 있지만 Blazor Weasembmbveris는 미리 보기로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="93fde-109">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="93fde-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93fde-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="93fde-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-111">1\.</span></span> <span data-ttu-id="93fde-112">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="93fde-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-113">2\.</span></span> <span data-ttu-id="93fde-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-114">Create a new project.</span></span>

   <span data-ttu-id="93fde-115">3.</span><span class="sxs-lookup"><span data-stu-id="93fde-115">3\.</span></span> <span data-ttu-id="93fde-116">**Blazor App**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-116">Select **Blazor App**.</span></span> <span data-ttu-id="93fde-117">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-117">Select **Next**.</span></span>

   <span data-ttu-id="93fde-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-118">4\.</span></span> <span data-ttu-id="93fde-119">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="93fde-120">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="93fde-121">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-121">Select **Create**.</span></span>

   <span data-ttu-id="93fde-122">5.</span><span class="sxs-lookup"><span data-stu-id="93fde-122">5\.</span></span> <span data-ttu-id="93fde-123">Blazor weasembomexperience 환경에 대해 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="93fde-124">Blazor 서버 환경의 경우 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="93fde-125">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-125">Select **Create**.</span></span> <span data-ttu-id="93fde-126">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembmbambmbsembmbsemba*<xref:blazor/hosting-models>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93fde-127">6.</span><span class="sxs-lookup"><span data-stu-id="93fde-127">6\.</span></span> <span data-ttu-id="93fde-128">**F5** 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-128">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="93fde-129">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대해 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="93fde-130">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="93fde-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93fde-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="93fde-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-132">1\.</span></span> <span data-ttu-id="93fde-133">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="93fde-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-134">2\.</span></span> <span data-ttu-id="93fde-135">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="93fde-136">3.</span><span class="sxs-lookup"><span data-stu-id="93fde-136">3\.</span></span> <span data-ttu-id="93fde-137">Blazor Weasembomexperience 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="93fde-138">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="93fde-139">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembmbambmbsembmbsemba*<xref:blazor/hosting-models>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93fde-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-140">4\.</span></span> <span data-ttu-id="93fde-141">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="93fde-142">5.</span><span class="sxs-lookup"><span data-stu-id="93fde-142">5\.</span></span> <span data-ttu-id="93fde-143">Blazor 서버 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="93fde-144">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-144">Select **Yes**.</span></span>

   <span data-ttu-id="93fde-145">6.</span><span class="sxs-lookup"><span data-stu-id="93fde-145">6\.</span></span> <span data-ttu-id="93fde-146">Blazor 서버 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="93fde-147">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="93fde-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-148">7\.</span></span> <span data-ttu-id="93fde-149">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="93fde-150">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="93fde-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="93fde-151">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93fde-152">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93fde-153">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembmbambmbsembmbsemba*<xref:blazor/hosting-models>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93fde-154">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="93fde-155">최신 [.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 릴리스를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="93fde-156">필요에 따라 [.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) 를 설치한 다음 명령 셸에서 다음 명령을 실행 하 여 [Blazor Weasembomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="93fde-157">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="93fde-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93fde-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="93fde-159">1 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-159">1\.</span></span> <span data-ttu-id="93fde-160">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 최신 [Visual Studio](https://visualstudio.com/vs/) 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="93fde-161">2 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-161">2\.</span></span> <span data-ttu-id="93fde-162">필요에 따라 Blazor **weASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="93fde-163">3.</span><span class="sxs-lookup"><span data-stu-id="93fde-163">3\.</span></span> <span data-ttu-id="93fde-164">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-164">Create a new project.</span></span>

   <span data-ttu-id="93fde-165">4 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-165">4\.</span></span> <span data-ttu-id="93fde-166">**Blazor App**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-166">Select **Blazor App**.</span></span> <span data-ttu-id="93fde-167">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-167">Select **Next**.</span></span>

   <span data-ttu-id="93fde-168">5.</span><span class="sxs-lookup"><span data-stu-id="93fde-168">5\.</span></span> <span data-ttu-id="93fde-169">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="93fde-170">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="93fde-171">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-171">Select **Create**.</span></span>

   <span data-ttu-id="93fde-172">6.</span><span class="sxs-lookup"><span data-stu-id="93fde-172">6\.</span></span> <span data-ttu-id="93fde-173">Blazor weasembomexperience 환경에 대해 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="93fde-174">Blazor 서버 환경의 경우 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="93fde-175">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-175">Select **Create**.</span></span> <span data-ttu-id="93fde-176">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembmbambmbsembmbsemba*<xref:blazor/hosting-models>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93fde-177">7 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-177">7\.</span></span> <span data-ttu-id="93fde-178">**F5** 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="93fde-179">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대해 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="93fde-180">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="93fde-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93fde-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="93fde-182">1 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-182">1\.</span></span> <span data-ttu-id="93fde-183">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="93fde-184">2 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-184">2\.</span></span> <span data-ttu-id="93fde-185">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="93fde-186">3.</span><span class="sxs-lookup"><span data-stu-id="93fde-186">3\.</span></span> <span data-ttu-id="93fde-187">Blazor Weasembomexperience 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="93fde-188">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="93fde-189">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembmbambmbsembmbsemba*<xref:blazor/hosting-models>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93fde-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-190">4\.</span></span> <span data-ttu-id="93fde-191">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="93fde-192">5.</span><span class="sxs-lookup"><span data-stu-id="93fde-192">5\.</span></span> <span data-ttu-id="93fde-193">Blazor 서버 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="93fde-194">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-194">Select **Yes**.</span></span>

   <span data-ttu-id="93fde-195">6.</span><span class="sxs-lookup"><span data-stu-id="93fde-195">6\.</span></span> <span data-ttu-id="93fde-196">Blazor 서버 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="93fde-197">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="93fde-198">7 \.</span><span class="sxs-lookup"><span data-stu-id="93fde-198">7\.</span></span> <span data-ttu-id="93fde-199">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-199">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="93fde-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="93fde-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="93fde-201">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93fde-202">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="93fde-203">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembmbambmbsembmbsemba*<xref:blazor/hosting-models>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="93fde-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="93fde-204">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="93fde-205">세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="93fde-206">홈</span><span class="sxs-lookup"><span data-stu-id="93fde-206">Home</span></span>
* <span data-ttu-id="93fde-207">카운터</span><span class="sxs-lookup"><span data-stu-id="93fde-207">Counter</span></span>
* <span data-ttu-id="93fde-208">데이터 페치</span><span class="sxs-lookup"><span data-stu-id="93fde-208">Fetch data</span></span>

<span data-ttu-id="93fde-209">카운터 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증분합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="93fde-210">웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만 Blazor을 사용 C#하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="93fde-211">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="93fde-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="93fde-212">브라우저에서 `/counter`에 대 한 요청은 맨 위에 있는 `@page` 지시문에 지정 된 대로 `Counter` 구성 요소가 해당 내용을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="93fde-213">구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="93fde-214">**[클릭** ] 단추를 선택할 때마다:</span><span class="sxs-lookup"><span data-stu-id="93fde-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="93fde-215">@No__t-0 이벤트가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="93fde-216">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="93fde-217">@No__t-0이 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="93fde-218">구성 요소가 다시 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-218">The component is rendered again.</span></span>

<span data-ttu-id="93fde-219">런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="93fde-220">HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="93fde-221">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 `Counter` 구성 요소를 앱 홈 페이지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="93fde-222">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="93fde-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="93fde-223">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-223">Run the app.</span></span> <span data-ttu-id="93fde-224">홈페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="93fde-225">구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="93fde-226">@No__t-0 구성 요소에 매개 변수를 추가 하려면 구성 요소의 `@code` 블록을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="93fde-227">@No__t-1 특성을 사용 하 여 `IncrementAmount`에 대 한 public 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="93fde-228">`currentCount` 값을 늘릴 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="93fde-229">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="93fde-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="93fde-230">특성을 사용 하 여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="93fde-231">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="93fde-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="93fde-232">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-232">Run the app.</span></span> <span data-ttu-id="93fde-233">@No__t-0 구성 요소에는 **클릭** 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="93fde-234">@No__t-2의 `Counter` 구성 요소 (*. razor*)는 1 씩 계속 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="93fde-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="93fde-235">다음 단계</span><span class="sxs-lookup"><span data-stu-id="93fde-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="93fde-236">추가 자료</span><span class="sxs-lookup"><span data-stu-id="93fde-236">Additional resources</span></span>

* <xref:signalr/introduction>
