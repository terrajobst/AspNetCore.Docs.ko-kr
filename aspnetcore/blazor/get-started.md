---
title: ASP.NET Core Blazor 시작 하기
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 하세요.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/get-started
ms.openlocfilehash: 4c2a8f62b7f6a60815d131756d1e551904d918ad
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207217"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="96d4f-103">ASP.NET Core Blazor 시작 하기</span><span class="sxs-lookup"><span data-stu-id="96d4f-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="96d4f-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="96d4f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="96d4f-105">Blazor 시작 하기:</span><span class="sxs-lookup"><span data-stu-id="96d4f-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="96d4f-106">최신 [.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 릴리스를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-106">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="96d4f-107">명령 셸에서 다음 명령을 실행 하 여 Blazor 템플릿을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19465.2
   ```

1. <span data-ttu-id="96d4f-108">선택한 도구에 대 한 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="96d4f-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="96d4f-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96d4f-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="96d4f-110">1 \.</span><span class="sxs-lookup"><span data-stu-id="96d4f-110">1\.</span></span> <span data-ttu-id="96d4f-111">**ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 최신 [Visual Studio](https://visualstudio.com/vs/) 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-111">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="96d4f-112">2 \.</span><span class="sxs-lookup"><span data-stu-id="96d4f-112">2\.</span></span> <span data-ttu-id="96d4f-113">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-113">Create a new project.</span></span>

   <span data-ttu-id="96d4f-114">3\.</span><span class="sxs-lookup"><span data-stu-id="96d4f-114">3\.</span></span> <span data-ttu-id="96d4f-115">**Blazor App**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-115">Select **Blazor App**.</span></span> <span data-ttu-id="96d4f-116">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-116">Select **Next**.</span></span>

   <span data-ttu-id="96d4f-117">4 \.</span><span class="sxs-lookup"><span data-stu-id="96d4f-117">4\.</span></span> <span data-ttu-id="96d4f-118">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="96d4f-119">**위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="96d4f-120">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-120">Select **Create**.</span></span>

   <span data-ttu-id="96d4f-121">5\.</span><span class="sxs-lookup"><span data-stu-id="96d4f-121">5\.</span></span> <span data-ttu-id="96d4f-122">Blazor weasembomexperience 환경에 대해 **Blazor Weasembomapp** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-122">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="96d4f-123">Blazor 서버 환경의 경우 **Blazor Server 앱** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-123">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="96d4f-124">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-124">Select **Create**.</span></span> <span data-ttu-id="96d4f-125">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembomommbmbsembmbsembmbsembse.* <xref:blazor/hosting-models></span><span class="sxs-lookup"><span data-stu-id="96d4f-125">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="96d4f-126">6\.</span><span class="sxs-lookup"><span data-stu-id="96d4f-126">6\.</span></span> <span data-ttu-id="96d4f-127">**F5** 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="96d4f-128">ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대해 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="96d4f-129">이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="96d4f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96d4f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="96d4f-131">1 \.</span><span class="sxs-lookup"><span data-stu-id="96d4f-131">1\.</span></span> <span data-ttu-id="96d4f-132">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="96d4f-133">2 \.</span><span class="sxs-lookup"><span data-stu-id="96d4f-133">2\.</span></span> <span data-ttu-id="96d4f-134">[ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="96d4f-135">3\.</span><span class="sxs-lookup"><span data-stu-id="96d4f-135">3\.</span></span> <span data-ttu-id="96d4f-136">Blazor Weasembomexperience 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-136">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="96d4f-137">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-137">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="96d4f-138">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembomommbmbsembmbsembmbsembse.* <xref:blazor/hosting-models></span><span class="sxs-lookup"><span data-stu-id="96d4f-138">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="96d4f-139">4 \.</span><span class="sxs-lookup"><span data-stu-id="96d4f-139">4\.</span></span> <span data-ttu-id="96d4f-140">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="96d4f-141">5\.</span><span class="sxs-lookup"><span data-stu-id="96d4f-141">5\.</span></span> <span data-ttu-id="96d4f-142">Blazor 서버 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-142">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="96d4f-143">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-143">Select **Yes**.</span></span>

   <span data-ttu-id="96d4f-144">6\.</span><span class="sxs-lookup"><span data-stu-id="96d4f-144">6\.</span></span> <span data-ttu-id="96d4f-145">Blazor 서버 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-145">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="96d4f-146">Blazor weasembomapp을 사용 하는 경우 `dotnet run` 앱의 프로젝트 폴더에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-146">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="96d4f-147">7 \.</span><span class="sxs-lookup"><span data-stu-id="96d4f-147">7\.</span></span> <span data-ttu-id="96d4f-148">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-148">In a browser, navigate to `https://localhost:5001`.</span></span>

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

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="96d4f-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="96d4f-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="96d4f-150">Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-150">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="96d4f-151">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-151">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="96d4f-152">두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembomommbmbsembmbsembmbsembse.* <xref:blazor/hosting-models></span><span class="sxs-lookup"><span data-stu-id="96d4f-152">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="96d4f-153">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="96d4f-154">세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="96d4f-155">홈</span><span class="sxs-lookup"><span data-stu-id="96d4f-155">Home</span></span>
* <span data-ttu-id="96d4f-156">카운터</span><span class="sxs-lookup"><span data-stu-id="96d4f-156">Counter</span></span>
* <span data-ttu-id="96d4f-157">데이터 페치</span><span class="sxs-lookup"><span data-stu-id="96d4f-157">Fetch data</span></span>

<span data-ttu-id="96d4f-158">카운터 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증분합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="96d4f-159">웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만 Razor 구성 요소는을 사용 하 C#여 더 나은 접근 방식을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="96d4f-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="96d4f-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="96d4f-161">맨 위에 `/counter` 있는지시문`@page` 에 지정 된 대로 브라우저에서에 대 한 요청은 구성요소가해당콘텐츠를렌더링하도록합니다.`Counter`</span><span class="sxs-lookup"><span data-stu-id="96d4f-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="96d4f-162">구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="96d4f-163">**[클릭** ] 단추를 선택할 때마다:</span><span class="sxs-lookup"><span data-stu-id="96d4f-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="96d4f-164">`onclick` 이벤트가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="96d4f-165">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="96d4f-166">`currentCount` 가 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="96d4f-167">구성 요소가 다시 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-167">The component is rendered again.</span></span>

<span data-ttu-id="96d4f-168">런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="96d4f-169">HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="96d4f-170">예를 들어 `Counter` `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 응용 프로그램의 홈페이지에 구성 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="96d4f-171">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="96d4f-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="96d4f-172">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-172">Run the app.</span></span> <span data-ttu-id="96d4f-173">홈 페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="96d4f-174">구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="96d4f-175">`Counter` 구성 요소에 매개 변수를 추가 하려면 `@code` 구성 요소의 블록을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="96d4f-176">특성`[Parameter]` 을 사용 하 여 `IncrementAmount` 에 대 한 public 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="96d4f-177">`currentCount` 값을 늘릴 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="96d4f-178">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="96d4f-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="96d4f-179">특성을 `IncrementAmount` 사용 하 `Index` 여 구성 `<Counter>` 요소의 요소에를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="96d4f-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="96d4f-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="96d4f-181">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-181">Run the app.</span></span> <span data-ttu-id="96d4f-182">구성 요소에는 클릭 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다. `Index`</span><span class="sxs-lookup"><span data-stu-id="96d4f-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="96d4f-183">의 `Counter` 구성 요소 ( `/counter` *카운터. razor*)는 계속 해 서 1 씩 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="96d4f-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="96d4f-184">다음 단계</span><span class="sxs-lookup"><span data-stu-id="96d4f-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="96d4f-185">추가 자료</span><span class="sxs-lookup"><span data-stu-id="96d4f-185">Additional resources</span></span>

* <xref:signalr/introduction>
