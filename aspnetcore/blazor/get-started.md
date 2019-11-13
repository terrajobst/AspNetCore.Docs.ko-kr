---
title: ASP.NET Core Blazor 시작
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 9b4aee0be30568f098c756e9ab4cb5298e9a049b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963005"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="6cf8b-103">ASP.NET Core Blazor 시작</span><span class="sxs-lookup"><span data-stu-id="6cf8b-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="6cf8b-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6cf8b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6cf8b-105">Blazor시작:</span><span class="sxs-lookup"><span data-stu-id="6cf8b-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="6cf8b-106">[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="6cf8b-107">명령 셸에서 다음 명령을 실행 하 여 [Blazor Weasembomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="6cf8b-108">[AspNetCoreBlazor입니다. 템플릿](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 있지만 Blazor Weasembmbveris는 미리 보기로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. <span data-ttu-id="6cf8b-109">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6cf8b-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6cf8b-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="6cf8b-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-111">1\.</span></span> <span data-ttu-id="6cf8b-112">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="6cf8b-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-113">2\.</span></span> <span data-ttu-id="6cf8b-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-114">Create a new project.</span></span>

   <span data-ttu-id="6cf8b-115">3\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-115">3\.</span></span> <span data-ttu-id="6cf8b-116">**Blazor 앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-116">Select **Blazor App**.</span></span> <span data-ttu-id="6cf8b-117">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-117">Select **Next**.</span></span>

   <span data-ttu-id="6cf8b-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-118">4\.</span></span> <span data-ttu-id="6cf8b-119">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="6cf8b-120">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6cf8b-121">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-121">Select **Create**.</span></span>

   <span data-ttu-id="6cf8b-122">5\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-122">5\.</span></span> <span data-ttu-id="6cf8b-123">Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6cf8b-124">Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="6cf8b-125">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-125">Select **Create**.</span></span> <span data-ttu-id="6cf8b-126">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6cf8b-127">6\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-127">6\.</span></span> <span data-ttu-id="6cf8b-128">**Ctrl**+**F5**를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="6cf8b-129">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="6cf8b-130">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6cf8b-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6cf8b-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="6cf8b-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-132">1\.</span></span> <span data-ttu-id="6cf8b-133">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="6cf8b-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-134">2\.</span></span> <span data-ttu-id="6cf8b-135">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="6cf8b-136">3\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-136">3\.</span></span> <span data-ttu-id="6cf8b-137">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="6cf8b-138">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="6cf8b-139">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6cf8b-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-140">4\.</span></span> <span data-ttu-id="6cf8b-141">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="6cf8b-142">5\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-142">5\.</span></span> <span data-ttu-id="6cf8b-143">Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="6cf8b-144">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-144">Select **Yes**.</span></span>

   <span data-ttu-id="6cf8b-145">6\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-145">6\.</span></span> <span data-ttu-id="6cf8b-146">Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="6cf8b-147">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="6cf8b-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-148">7\.</span></span> <span data-ttu-id="6cf8b-149">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-149">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6cf8b-150">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6cf8b-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="6cf8b-151">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6cf8b-152">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6cf8b-153">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6cf8b-154">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="6cf8b-155">최신 [.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 릴리스를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="6cf8b-156">필요에 따라 [.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) 를 설치 하 고 명령 셸에서 다음 명령을 실행 하 여 [Blazor Weasembomtemplate](xref:blazor/hosting-models#blazor-webassembly) 템플릿을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. <span data-ttu-id="6cf8b-157">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6cf8b-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6cf8b-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="6cf8b-159">1 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-159">1\.</span></span> <span data-ttu-id="6cf8b-160">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 최신 [Visual Studio](https://visualstudio.com/vs/) 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="6cf8b-161">2 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-161">2\.</span></span> <span data-ttu-id="6cf8b-162">필요 Blazor에 따라 **ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="6cf8b-163">3\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-163">3\.</span></span> <span data-ttu-id="6cf8b-164">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-164">Create a new project.</span></span>

   <span data-ttu-id="6cf8b-165">4 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-165">4\.</span></span> <span data-ttu-id="6cf8b-166">**Blazor 앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-166">Select **Blazor App**.</span></span> <span data-ttu-id="6cf8b-167">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-167">Select **Next**.</span></span>

   <span data-ttu-id="6cf8b-168">5\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-168">5\.</span></span> <span data-ttu-id="6cf8b-169">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="6cf8b-170">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6cf8b-171">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-171">Select **Create**.</span></span>

   <span data-ttu-id="6cf8b-172">6\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-172">6\.</span></span> <span data-ttu-id="6cf8b-173">Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6cf8b-174">Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="6cf8b-175">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-175">Select **Create**.</span></span> <span data-ttu-id="6cf8b-176">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6cf8b-177">7 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-177">7\.</span></span> <span data-ttu-id="6cf8b-178">**F5** 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="6cf8b-179">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="6cf8b-180">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6cf8b-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6cf8b-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="6cf8b-182">1 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-182">1\.</span></span> <span data-ttu-id="6cf8b-183">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="6cf8b-184">2 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-184">2\.</span></span> <span data-ttu-id="6cf8b-185">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="6cf8b-186">3\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-186">3\.</span></span> <span data-ttu-id="6cf8b-187">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="6cf8b-188">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="6cf8b-189">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6cf8b-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-190">4\.</span></span> <span data-ttu-id="6cf8b-191">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="6cf8b-192">5\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-192">5\.</span></span> <span data-ttu-id="6cf8b-193">Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="6cf8b-194">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-194">Select **Yes**.</span></span>

   <span data-ttu-id="6cf8b-195">6\.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-195">6\.</span></span> <span data-ttu-id="6cf8b-196">Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="6cf8b-197">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="6cf8b-198">7 \.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-198">7\.</span></span> <span data-ttu-id="6cf8b-199">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-199">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6cf8b-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6cf8b-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="6cf8b-201">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6cf8b-202">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="6cf8b-203">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="6cf8b-204">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="6cf8b-205">세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="6cf8b-206">홈</span><span class="sxs-lookup"><span data-stu-id="6cf8b-206">Home</span></span>
* <span data-ttu-id="6cf8b-207">카운터</span><span class="sxs-lookup"><span data-stu-id="6cf8b-207">Counter</span></span>
* <span data-ttu-id="6cf8b-208">데이터 페치</span><span class="sxs-lookup"><span data-stu-id="6cf8b-208">Fetch data</span></span>

<span data-ttu-id="6cf8b-209">Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="6cf8b-210">웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만를 사용할 C#수 Blazor.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="6cf8b-211">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6cf8b-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="6cf8b-212">브라우저에서 `/counter`에 대 한 요청은 맨 위에 있는 `@page` 지시문에 지정 된 대로 `Counter` 구성 요소가 해당 내용을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="6cf8b-213">구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="6cf8b-214">**[클릭** ] 단추를 선택할 때마다:</span><span class="sxs-lookup"><span data-stu-id="6cf8b-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="6cf8b-215">`onclick` 이벤트가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="6cf8b-216">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="6cf8b-217">`currentCount` 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="6cf8b-218">구성 요소가 다시 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-218">The component is rendered again.</span></span>

<span data-ttu-id="6cf8b-219">런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="6cf8b-220">HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="6cf8b-221">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 `Counter` 구성 요소를 앱 홈 페이지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="6cf8b-222">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6cf8b-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="6cf8b-223">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-223">Run the app.</span></span> <span data-ttu-id="6cf8b-224">홈페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="6cf8b-225">구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="6cf8b-226">`Counter` 구성 요소에 매개 변수를 추가 하려면 구성 요소의 `@code` 블록을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="6cf8b-227">`[Parameter]` 특성을 사용 하 여 `IncrementAmount`에 대 한 public 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="6cf8b-228">`currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="6cf8b-229">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6cf8b-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="6cf8b-230">특성을 사용 하 여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="6cf8b-231">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6cf8b-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="6cf8b-232">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-232">Run the app.</span></span> <span data-ttu-id="6cf8b-233">`Index` 구성 요소에는 **클릭** 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="6cf8b-234">`/counter`의 `Counter` 구성 요소 ( *. razor*)는 1 씩 계속 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf8b-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6cf8b-235">다음 단계</span><span class="sxs-lookup"><span data-stu-id="6cf8b-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="6cf8b-236">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6cf8b-236">Additional resources</span></span>

* <xref:signalr/introduction>
