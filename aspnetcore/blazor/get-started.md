---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 09400a076849bdec35beb284a488d01feb8a84c2
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160004"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="43977-103">ASP.NET Core Blazor 시작하기</span><span class="sxs-lookup"><span data-stu-id="43977-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="43977-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43977-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="43977-105">Blazor시작:</span><span class="sxs-lookup"><span data-stu-id="43977-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="43977-106">[.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="43977-107">필요에 따라 [Blazor Weasembomomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="43977-108">[.Net Core 3.1 이상 (미리 보기) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="43977-109">명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-109">Run the following command in a command shell.</span></span> <span data-ttu-id="43977-110">[AspNetCoreBlazor입니다. 템플릿](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 있지만 Blazor Weasembmbveris는 미리 보기로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43977-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="43977-111">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="43977-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43977-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43977-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="43977-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="43977-113">1\.</span></span> <span data-ttu-id="43977-114">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 16.4 이상 버전](https://visualstudio.microsoft.com/vs/preview/) 을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="43977-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="43977-115">2\.</span></span> <span data-ttu-id="43977-116">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="43977-116">Create a new project.</span></span>

   <span data-ttu-id="43977-117">3\.</span><span class="sxs-lookup"><span data-stu-id="43977-117">3\.</span></span> <span data-ttu-id="43977-118">**Blazor 앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-118">Select **Blazor App**.</span></span> <span data-ttu-id="43977-119">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-119">Select **Next**.</span></span>

   <span data-ttu-id="43977-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="43977-120">4\.</span></span> <span data-ttu-id="43977-121">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="43977-122">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="43977-123">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-123">Select **Create**.</span></span>

   <span data-ttu-id="43977-124">5\.</span><span class="sxs-lookup"><span data-stu-id="43977-124">5\.</span></span> <span data-ttu-id="43977-125">Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="43977-126">Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="43977-127">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-127">Select **Create**.</span></span> <span data-ttu-id="43977-128">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43977-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="43977-129">6\.</span><span class="sxs-lookup"><span data-stu-id="43977-129">6\.</span></span> <span data-ttu-id="43977-130">**Ctrl**+**F5**를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="43977-131">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43977-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="43977-132">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="43977-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43977-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="43977-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="43977-134">1\.</span></span> <span data-ttu-id="43977-135">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="43977-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="43977-136">2\.</span></span> <span data-ttu-id="43977-137">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="43977-138">3\.</span><span class="sxs-lookup"><span data-stu-id="43977-138">3\.</span></span> <span data-ttu-id="43977-139">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="43977-140">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="43977-141">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43977-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="43977-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="43977-142">4\.</span></span> <span data-ttu-id="43977-143">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="43977-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="43977-144">5\.</span><span class="sxs-lookup"><span data-stu-id="43977-144">5\.</span></span> <span data-ttu-id="43977-145">Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="43977-146">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-146">Select **Yes**.</span></span>

   <span data-ttu-id="43977-147">6\.</span><span class="sxs-lookup"><span data-stu-id="43977-147">6\.</span></span> <span data-ttu-id="43977-148">Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="43977-149">Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="43977-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="43977-150">7\.</span></span> <span data-ttu-id="43977-151">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="43977-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="43977-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="43977-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="43977-153">1\.</span></span> <span data-ttu-id="43977-154">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="43977-155">2 \.</span><span class="sxs-lookup"><span data-stu-id="43977-155">2\.</span></span> <span data-ttu-id="43977-156">**파일** > **새 솔루션** 을 선택 하거나 **새 프로젝트**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="43977-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="43977-157">3\.</span><span class="sxs-lookup"><span data-stu-id="43977-157">3\.</span></span> <span data-ttu-id="43977-158">사이드바에서 **.Net Core** > **앱**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="43977-159">4 \.</span><span class="sxs-lookup"><span data-stu-id="43977-159">4\.</span></span> <span data-ttu-id="43977-160">**Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="43977-161">현재 Mac용 Visual Studio에서는 Blazor 서버 템플릿만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43977-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="43977-162">Blazor Weasembomexperience의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="43977-163">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43977-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="43977-164">5\.</span><span class="sxs-lookup"><span data-stu-id="43977-164">5\.</span></span> <span data-ttu-id="43977-165">**대상 프레임 워크** 를 **.net Core 3.1** 로 설정 하 고 **다음**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="43977-166">6\.</span><span class="sxs-lookup"><span data-stu-id="43977-166">6\.</span></span> <span data-ttu-id="43977-167">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="43977-168">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-168">Select **Create**.</span></span>

   <span data-ttu-id="43977-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="43977-169">7\.</span></span> <span data-ttu-id="43977-170">**디버깅 하지 않고** 실행 > 실행을 선택 하 여 *디버거 없이*앱 **을 실행 합니다** .</span><span class="sxs-lookup"><span data-stu-id="43977-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="43977-171">**디버깅 시작** 을 사용 하 여 앱을 실행 하 고 *디버거를 사용*하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="43977-172">개발 인증서를 신뢰 하 라는 메시지가 표시 되 면 인증서를 신뢰 하 고 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43977-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="43977-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="43977-174">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="43977-175">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="43977-176">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="43977-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="43977-177">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="43977-178">세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43977-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="43977-179">홈</span><span class="sxs-lookup"><span data-stu-id="43977-179">Home</span></span>
* <span data-ttu-id="43977-180">카운터</span><span class="sxs-lookup"><span data-stu-id="43977-180">Counter</span></span>
* <span data-ttu-id="43977-181">데이터 페치</span><span class="sxs-lookup"><span data-stu-id="43977-181">Fetch data</span></span>

<span data-ttu-id="43977-182">카운터 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증분합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="43977-183">웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만를 사용할 C#수 Blazor.</span><span class="sxs-lookup"><span data-stu-id="43977-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="43977-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="43977-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="43977-185">맨 위에 있는 `@page` 지시문에 지정 된 대로 브라우저의 `/counter`에 대 한 요청은 `Counter` 구성 요소가 해당 내용을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="43977-186">구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43977-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="43977-187">**[클릭** ] 단추를 선택할 때마다:</span><span class="sxs-lookup"><span data-stu-id="43977-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="43977-188">`onclick` 이벤트가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="43977-189">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="43977-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="43977-190">`currentCount` 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="43977-191">구성 요소가 다시 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43977-191">The component is rendered again.</span></span>

<span data-ttu-id="43977-192">런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="43977-193">HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="43977-194">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 앱의 홈페이지에 `Counter` 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="43977-195">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="43977-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="43977-196">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-196">Run the app.</span></span> <span data-ttu-id="43977-197">홈페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43977-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="43977-198">구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43977-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="43977-199">`Counter` 구성 요소에 매개 변수를 추가 하려면 구성 요소의 `@code` 블록을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="43977-200">`[Parameter]` 특성을 사용 하 여 `IncrementAmount`에 대 한 public 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="43977-201">`currentCount` 값을 늘릴 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="43977-202">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="43977-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="43977-203">특성을 사용 하 여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="43977-204">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="43977-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="43977-205">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-205">Run the app.</span></span> <span data-ttu-id="43977-206">`Index` 구성 요소에는 **클릭** 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43977-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="43977-207">`/counter`의 `Counter` 구성 요소 ( *. razor*)는 1 씩 계속 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="43977-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="43977-208">다음 단계</span><span class="sxs-lookup"><span data-stu-id="43977-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="43977-209">추가 자료</span><span class="sxs-lookup"><span data-stu-id="43977-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
