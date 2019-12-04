---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: d356a06849f54434c492dc68f57f7edc8805de22
ms.sourcegitcommit: 5974e3e66dab3398ecf2324fbb82a9c5636f70de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74778780"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="33ad0-103">ASP.NET Core Blazor 시작하기</span><span class="sxs-lookup"><span data-stu-id="33ad0-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="33ad0-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="33ad0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="33ad0-105">Blazor시작:</span><span class="sxs-lookup"><span data-stu-id="33ad0-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="33ad0-106">[.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="33ad0-107">필요에 따라 [Blazor Weasembomomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="33ad0-108">[.Net Core 3.1 이상 (미리 보기) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="33ad0-109">명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-109">Run the following command in a command shell.</span></span> <span data-ttu-id="33ad0-110">[AspNetCoreBlazor입니다. 템플릿](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 있지만 Blazor Weasembmbveris는 미리 보기로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="33ad0-111">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="33ad0-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33ad0-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="33ad0-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-113">1\.</span></span> <span data-ttu-id="33ad0-114">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 이상 버전](https://visualstudio.microsoft.com/vs/preview/) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="33ad0-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-115">2\.</span></span> <span data-ttu-id="33ad0-116">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-116">Create a new project.</span></span>

   <span data-ttu-id="33ad0-117">3\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-117">3\.</span></span> <span data-ttu-id="33ad0-118">**Blazor 앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-118">Select **Blazor App**.</span></span> <span data-ttu-id="33ad0-119">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-119">Select **Next**.</span></span>

   <span data-ttu-id="33ad0-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-120">4\.</span></span> <span data-ttu-id="33ad0-121">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="33ad0-122">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="33ad0-123">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-123">Select **Create**.</span></span>

   <span data-ttu-id="33ad0-124">5\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-124">5\.</span></span> <span data-ttu-id="33ad0-125">Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="33ad0-126">Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="33ad0-127">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-127">Select **Create**.</span></span> <span data-ttu-id="33ad0-128">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="33ad0-129">6\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-129">6\.</span></span> <span data-ttu-id="33ad0-130">**Ctrl**+**F5**를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="33ad0-131">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="33ad0-132">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="33ad0-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33ad0-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="33ad0-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-134">1\.</span></span> <span data-ttu-id="33ad0-135">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="33ad0-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-136">2\.</span></span> <span data-ttu-id="33ad0-137">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="33ad0-138">3\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-138">3\.</span></span> <span data-ttu-id="33ad0-139">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="33ad0-140">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="33ad0-141">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="33ad0-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-142">4\.</span></span> <span data-ttu-id="33ad0-143">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="33ad0-144">5\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-144">5\.</span></span> <span data-ttu-id="33ad0-145">Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="33ad0-146">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-146">Select **Yes**.</span></span>

   <span data-ttu-id="33ad0-147">6\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-147">6\.</span></span> <span data-ttu-id="33ad0-148">Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="33ad0-149">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="33ad0-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-150">7\.</span></span> <span data-ttu-id="33ad0-151">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="33ad0-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="33ad0-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="33ad0-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-153">1\.</span></span> <span data-ttu-id="33ad0-154">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="33ad0-155">[업데이트 채널을 미리 보기로](/visualstudio/mac/install-preview)전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-155">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="33ad0-156">2 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-156">2\.</span></span> <span data-ttu-id="33ad0-157">**파일** > **새 솔루션** 을 선택 하거나 **새 프로젝트**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-157">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="33ad0-158">3\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-158">3\.</span></span> <span data-ttu-id="33ad0-159">사이드바에서 **.Net Core** > **앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-159">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="33ad0-160">4 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-160">4\.</span></span> <span data-ttu-id="33ad0-161">**Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-161">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="33ad0-162">현재 Mac용 Visual Studio에서는 Blazor 서버 템플릿만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-162">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="33ad0-163">Blazor Weasembomexperience의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-163">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="33ad0-164">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-164">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="33ad0-165">5\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-165">5\.</span></span> <span data-ttu-id="33ad0-166">**대상 프레임 워크** 는 기본적으로 **.net core 3.0** (또는 3.1 Preview SDK가 설치 된 경우 **.net core 3.1** )로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-166">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="33ad0-167">프레임 워크를 선택 하 고 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-167">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="33ad0-168">6\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-168">6\.</span></span> <span data-ttu-id="33ad0-169">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-169">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="33ad0-170">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-170">Select **Create**.</span></span>

   <span data-ttu-id="33ad0-171">7 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-171">7\.</span></span> <span data-ttu-id="33ad0-172">**디버깅 하지 않고** 실행 > 실행을 선택 하 여 *디버거 없이*앱 **을 실행 합니다** .</span><span class="sxs-lookup"><span data-stu-id="33ad0-172">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="33ad0-173">**디버깅 시작** 을 사용 하 여 앱을 실행 하 고 *디버거를 사용*하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-173">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="33ad0-174">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33ad0-174">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="33ad0-175">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-175">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="33ad0-176">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-176">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="33ad0-177">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-177">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="33ad0-178">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-178">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="33ad0-179">최신 [.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-179">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="33ad0-180">필요에 따라 [Blazor Weasembomomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-180">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="33ad0-181">[.Net Core 3.1 이상 (미리 보기) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-181">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="33ad0-182">명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-182">Run the following command in a command shell.</span></span> <span data-ttu-id="33ad0-183">[AspNetCoreBlazor입니다. 템플릿](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 있지만 Blazor Weasembmbveris는 미리 보기로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-183">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="33ad0-184">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-184">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="33ad0-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33ad0-185">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="33ad0-186">1 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-186">1\.</span></span> <span data-ttu-id="33ad0-187">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 최신 [Visual Studio](https://visualstudio.com/vs/) 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-187">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="33ad0-188">2 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-188">2\.</span></span> <span data-ttu-id="33ad0-189">필요 Blazor에 따라 **ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 Preview 2](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-189">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="33ad0-190">3\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-190">3\.</span></span> <span data-ttu-id="33ad0-191">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-191">Create a new project.</span></span>

   <span data-ttu-id="33ad0-192">4 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-192">4\.</span></span> <span data-ttu-id="33ad0-193">**Blazor 앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-193">Select **Blazor App**.</span></span> <span data-ttu-id="33ad0-194">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-194">Select **Next**.</span></span>

   <span data-ttu-id="33ad0-195">5\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-195">5\.</span></span> <span data-ttu-id="33ad0-196">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-196">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="33ad0-197">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-197">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="33ad0-198">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-198">Select **Create**.</span></span>

   <span data-ttu-id="33ad0-199">6\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-199">6\.</span></span> <span data-ttu-id="33ad0-200">Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-200">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="33ad0-201">Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-201">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="33ad0-202">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-202">Select **Create**.</span></span> <span data-ttu-id="33ad0-203">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="33ad0-204">7 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-204">7\.</span></span> <span data-ttu-id="33ad0-205">**F5** 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-205">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="33ad0-206">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-206">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="33ad0-207">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-207">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="33ad0-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33ad0-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="33ad0-209">1 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-209">1\.</span></span> <span data-ttu-id="33ad0-210">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-210">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="33ad0-211">2 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-211">2\.</span></span> <span data-ttu-id="33ad0-212">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-212">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="33ad0-213">3\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-213">3\.</span></span> <span data-ttu-id="33ad0-214">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-214">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="33ad0-215">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-215">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="33ad0-216">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-216">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="33ad0-217">4 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-217">4\.</span></span> <span data-ttu-id="33ad0-218">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-218">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="33ad0-219">5\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-219">5\.</span></span> <span data-ttu-id="33ad0-220">Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-220">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="33ad0-221">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-221">Select **Yes**.</span></span>

   <span data-ttu-id="33ad0-222">6\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-222">6\.</span></span> <span data-ttu-id="33ad0-223">Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-223">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="33ad0-224">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-224">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="33ad0-225">7 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-225">7\.</span></span> <span data-ttu-id="33ad0-226">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-226">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="33ad0-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="33ad0-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="33ad0-228">1 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-228">1\.</span></span> <span data-ttu-id="33ad0-229">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-229">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="33ad0-230">[업데이트 채널을 미리 보기로](/visualstudio/mac/install-preview)전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-230">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="33ad0-231">2 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-231">2\.</span></span> <span data-ttu-id="33ad0-232">**파일** > **새 솔루션** 을 선택 하거나 **새 프로젝트**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-232">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="33ad0-233">3\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-233">3\.</span></span> <span data-ttu-id="33ad0-234">사이드바에서 **.Net Core** > **앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-234">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="33ad0-235">4 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-235">4\.</span></span> <span data-ttu-id="33ad0-236">**Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-236">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="33ad0-237">현재 Mac용 Visual Studio에서는 Blazor 서버 템플릿만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-237">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="33ad0-238">Blazor Weasembomexperience의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-238">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="33ad0-239">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-239">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="33ad0-240">5\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-240">5\.</span></span> <span data-ttu-id="33ad0-241">**대상 프레임 워크** 는 기본적으로 **.net core 3.0** (또는 3.1 Preview SDK가 설치 된 경우 **.net core 3.1** )로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-241">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="33ad0-242">프레임 워크를 선택 하 고 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-242">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="33ad0-243">6\.</span><span class="sxs-lookup"><span data-stu-id="33ad0-243">6\.</span></span> <span data-ttu-id="33ad0-244">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-244">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="33ad0-245">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-245">Select **Create**.</span></span>

   <span data-ttu-id="33ad0-246">7 \.</span><span class="sxs-lookup"><span data-stu-id="33ad0-246">7\.</span></span> <span data-ttu-id="33ad0-247">**디버깅 하지 않고** 실행 > 실행을 선택 하 여 *디버거 없이*앱 **을 실행 합니다** .</span><span class="sxs-lookup"><span data-stu-id="33ad0-247">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="33ad0-248">**디버깅 시작** 을 사용 하 여 앱을 실행 하 고 *디버거를 사용*하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-248">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="33ad0-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33ad0-249">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="33ad0-250">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-250">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="33ad0-251">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-251">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="33ad0-252">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="33ad0-252">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="33ad0-253">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-253">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="33ad0-254">세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-254">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="33ad0-255">홈</span><span class="sxs-lookup"><span data-stu-id="33ad0-255">Home</span></span>
* <span data-ttu-id="33ad0-256">카운터</span><span class="sxs-lookup"><span data-stu-id="33ad0-256">Counter</span></span>
* <span data-ttu-id="33ad0-257">데이터 페치</span><span class="sxs-lookup"><span data-stu-id="33ad0-257">Fetch data</span></span>

<span data-ttu-id="33ad0-258">Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-258">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="33ad0-259">웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만를 사용할 C#수 Blazor.</span><span class="sxs-lookup"><span data-stu-id="33ad0-259">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="33ad0-260">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="33ad0-260">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="33ad0-261">맨 위에 있는 `@page` 지시문에 지정 된 대로 브라우저의 `/counter`에 대 한 요청은 `Counter` 구성 요소가 해당 내용을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-261">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="33ad0-262">구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-262">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="33ad0-263">**[클릭** ] 단추를 선택할 때마다:</span><span class="sxs-lookup"><span data-stu-id="33ad0-263">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="33ad0-264">`onclick` 이벤트가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-264">The `onclick` event is fired.</span></span>
* <span data-ttu-id="33ad0-265">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-265">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="33ad0-266">`currentCount` 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-266">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="33ad0-267">구성 요소가 다시 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-267">The component is rendered again.</span></span>

<span data-ttu-id="33ad0-268">런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-268">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="33ad0-269">HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-269">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="33ad0-270">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 앱의 홈페이지에 `Counter` 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-270">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="33ad0-271">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="33ad0-271">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="33ad0-272">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-272">Run the app.</span></span> <span data-ttu-id="33ad0-273">홈페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-273">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="33ad0-274">구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-274">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="33ad0-275">`Counter` 구성 요소에 매개 변수를 추가 하려면 구성 요소의 `@code` 블록을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-275">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="33ad0-276">`[Parameter]` 특성을 사용 하 여 `IncrementAmount`에 대 한 public 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-276">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="33ad0-277">`currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-277">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="33ad0-278">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="33ad0-278">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="33ad0-279">특성을 사용 하 여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-279">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="33ad0-280">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="33ad0-280">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="33ad0-281">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-281">Run the app.</span></span> <span data-ttu-id="33ad0-282">`Index` 구성 요소에는 **클릭** 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-282">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="33ad0-283">`/counter`의 `Counter` 구성 요소 ( *. razor*)는 1 씩 계속 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="33ad0-283">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="33ad0-284">다음 단계</span><span class="sxs-lookup"><span data-stu-id="33ad0-284">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="33ad0-285">추가 자료</span><span class="sxs-lookup"><span data-stu-id="33ad0-285">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
