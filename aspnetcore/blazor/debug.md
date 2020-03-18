---
title: ASP.NET Core Blazor 디버그
author: guardrex
description: Blazor 앱을 디버그하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648315"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="07ea8-103">ASP.NET Core Blazor 디버그</span><span class="sxs-lookup"><span data-stu-id="07ea8-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="07ea8-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="07ea8-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="07ea8-105">Chromium 기반 브라우저(Chrome/Edge)에서 브라우저 개발 도구를 사용하여 Blazor WebAssembly를 디버깅하도록 하는 ‘초기’ 지원이 있습니다. </span><span class="sxs-lookup"><span data-stu-id="07ea8-105">*Early* support exists for debugging Blazor WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="07ea8-106">개발 중인 기능은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-106">Work is in progress to:</span></span>

* <span data-ttu-id="07ea8-107">Visual Studio에서 디버깅을 완전히 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="07ea8-108">Visual Studio Code에서 디버깅을 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="07ea8-109">디버거 기능은 제한적입니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="07ea8-110">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-110">Available scenarios include:</span></span>

* <span data-ttu-id="07ea8-111">중단점을 설정하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="07ea8-112">코드를 한 단계씩 실행(`F10`)하거나 코드 실행을 다시 시작(`F8`)합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="07ea8-113">‘지역’ 표시에서 `int`, `string` 및 `bool` 형식의 지역 변수 값을 확인합니다. </span><span class="sxs-lookup"><span data-stu-id="07ea8-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="07ea8-114">JavaScript에서 .NET으로 이동하고 .NET에서 JavaScript로 이동하는 호출 체인을 포함하여 호출 스택을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="07ea8-115">다음은 수행할 수 ‘없습니다’. </span><span class="sxs-lookup"><span data-stu-id="07ea8-115">You *can't*:</span></span>

* <span data-ttu-id="07ea8-116">`int`, `string` 또는 `bool`이 아닌 지역 변수의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="07ea8-117">클래스 속성 또는 필드의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="07ea8-118">변수를 마우스로 가리켜 해당 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="07ea8-119">콘솔에서 식을 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="07ea8-120">비동기 호출을 한 단계씩 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-120">Step across async calls.</span></span>
* <span data-ttu-id="07ea8-121">대부분의 다른 일반적인 디버깅 시나리오를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="07ea8-122">엔지니어링 팀은 추가 디버깅 시나리오 개발에 지속해서 주력하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07ea8-123">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="07ea8-123">Prerequisites</span></span>

<span data-ttu-id="07ea8-124">디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="07ea8-125">Google Chrome(버전 70 이상)</span><span class="sxs-lookup"><span data-stu-id="07ea8-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="07ea8-126">Microsoft Edge 미리 보기([Edge Dev 채널](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="07ea8-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="07ea8-127">프로시저</span><span class="sxs-lookup"><span data-stu-id="07ea8-127">Procedure</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07ea8-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07ea8-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="07ea8-129">Visual Studio의 디버깅 지원은 초기 개발 단계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="07ea8-130">**F5** 디버깅은 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="07ea8-131">디버깅하지 않고 `Debug` 구성에서 Blazor WebAssembly 앱을 실행합니다(**F5** 대신 **Ctrl**+**F5** 사용).</span><span class="sxs-lookup"><span data-stu-id="07ea8-131">Run a Blazor WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="07ea8-132">앱의 디버그 속성(**디버그** 메뉴의 마지막 항목)을 열고 HTTP **앱 URL**을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="07ea8-133">Chromium 기반 브라우저(Edge 베타 또는 Chrome)를 사용하여 앱의 HTTP 주소(HTTPS 주소 아님)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="07ea8-134">브라우저 창에서 개발자 도구 패널이 아니라 앱에 키보드 포커스를 놓습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="07ea8-135">이 절차에서는 개발자 도구 패널을 닫은 상태로 유지하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="07ea8-136">디버깅이 시작된 후 개발자 도구 패널을 다시 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="07ea8-137">다음과 같은 Blazor 특정 바로 가기 키를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-137">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="07ea8-138">Windows의 경우 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="07ea8-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="07ea8-139">macOS의 경우 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="07ea8-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="07ea8-140">**디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07ea8-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="07ea8-141">원격 디버깅을 사용하도록 설정한 후 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="07ea8-142">1\.</span><span class="sxs-lookup"><span data-stu-id="07ea8-142">1\.</span></span> <span data-ttu-id="07ea8-143">새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-143">A new browser window opens.</span></span> <span data-ttu-id="07ea8-144">이전 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-144">Close the prior window.</span></span>

   <span data-ttu-id="07ea8-145">2\.</span><span class="sxs-lookup"><span data-stu-id="07ea8-145">2\.</span></span> <span data-ttu-id="07ea8-146">브라우저 창에서 앱에 키보드 포커스를 놓습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="07ea8-147">3\.</span><span class="sxs-lookup"><span data-stu-id="07ea8-147">3\.</span></span> <span data-ttu-id="07ea8-148">새 브라우저 창에서 Blazor 특정 바로 가기 키(Windows의 경우 `Shift+Alt+D`, macOS의 경우 `Shift+Cmd+D`)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-148">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="07ea8-149">4\.</span><span class="sxs-lookup"><span data-stu-id="07ea8-149">4\.</span></span> <span data-ttu-id="07ea8-150">**DevTools** 탭이 브라우저에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="07ea8-151">**브라우저 창에서 앱의 탭을 다시 선택합니다.**</span><span class="sxs-lookup"><span data-stu-id="07ea8-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="07ea8-152">앱을 Visual Studio에 연결하려면 [Visual Studio에서 프로세스에 연결](#attach-to-process-in-visual-studio) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07ea8-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="07ea8-153">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="07ea8-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="07ea8-154">`--configuration Debug` 옵션을 [dotnet run](/dotnet/core/tools/dotnet-run) 명령에 전달(`dotnet run --configuration Debug`)하여 `Debug` 구성에서 Blazor WebAssembly 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-154">Run a Blazor WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="07ea8-155">셸의 창에 표시된 HTTP URL에 있는 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="07ea8-156">개발자 도구 패널이 아니라 앱에 키보드 포커스를 놓습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="07ea8-157">이 절차에서는 개발자 도구 패널을 닫은 상태로 유지하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="07ea8-158">디버깅이 시작된 후 개발자 도구 패널을 다시 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="07ea8-159">다음과 같은 Blazor 특정 바로 가기 키를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-159">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="07ea8-160">Windows의 경우 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="07ea8-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="07ea8-161">macOS의 경우 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="07ea8-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="07ea8-162">**디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07ea8-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="07ea8-163">원격 디버깅을 사용하도록 설정한 후 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="07ea8-164">1\.</span><span class="sxs-lookup"><span data-stu-id="07ea8-164">1\.</span></span> <span data-ttu-id="07ea8-165">새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-165">A new browser window opens.</span></span> <span data-ttu-id="07ea8-166">이전 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-166">Close the prior window.</span></span>

   <span data-ttu-id="07ea8-167">2\.</span><span class="sxs-lookup"><span data-stu-id="07ea8-167">2\.</span></span> <span data-ttu-id="07ea8-168">브라우저 창에서 개발자 도구 패널이 아니라 앱에 키보드 포커스를 놓습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="07ea8-169">3\.</span><span class="sxs-lookup"><span data-stu-id="07ea8-169">3\.</span></span> <span data-ttu-id="07ea8-170">새 브라우저 창에서 Blazor 특정 바로 가기 키(Windows의 경우 `Shift+Alt+D`, macOS의 경우 `Shift+Cmd+D`)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-170">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="07ea8-171">원격 디버깅 사용</span><span class="sxs-lookup"><span data-stu-id="07ea8-171">Enable remote debugging</span></span>

<span data-ttu-id="07ea8-172">원격 디버깅을 사용하지 않도록 설정한 경우, **디버그 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 Chrome에서 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="07ea8-173">오류 페이지에는 Blazor 디버깅 프록시가 앱에 연결할 수 있도록 디버깅 포트가 열린 상태로 Chrome을 실행하는 방법에 대한 지침이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="07ea8-174">‘Chrome 인스턴스를 모두 닫고’, 지침에 따라 Chrome을 다시 시작합니다. </span><span class="sxs-lookup"><span data-stu-id="07ea8-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="07ea8-175">앱 디버그</span><span class="sxs-lookup"><span data-stu-id="07ea8-175">Debug the app</span></span>

<span data-ttu-id="07ea8-176">원격 디버깅을 사용하도록 설정하여 Chrome을 실행한 후 디버깅 바로 가기 키를 누르면 새 디버거 탭이 열립니다. 잠시 후에 **소스** 탭에 앱의 .NET 어셈블리 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="07ea8-177">각 어셈블리를 펼쳐 디버깅에 사용할 수 있는 *.cs*/ *.razor* 소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="07ea8-178">중단점을 설정하고 앱의 탭으로 다시 전환한 다음, 코드를 실행하면 중단점이 적중됩니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="07ea8-179">중단점이 적중되면 코드를 한 단계씩 실행(`F10`)하거나 코드 실행을 정상적으로 다시 시작(`F8`)합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="07ea8-180">에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="07ea8-181">디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="07ea8-182">프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="07ea8-183">Visual Studio에서 프로세스에 연결</span><span class="sxs-lookup"><span data-stu-id="07ea8-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="07ea8-184">Visual Studio에서 앱의 프로세스에 연결하는 것은 **F5** 디버깅을 개발하는 동안 Blazor WebAssembly에 ‘임시로’ 사용되는 디버깅 시나리오입니다. </span><span class="sxs-lookup"><span data-stu-id="07ea8-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="07ea8-185">실행 중인 앱의 프로세스를 Visual Studio에 연결하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="07ea8-186">Visual Studio에서 **디버그** > **프로세스에 연결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="07ea8-187">**연결 형식**에서 **Chrome devtools 프로토콜 websocket**(인증 없음)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="07ea8-188">**연결 대상**에 앱의 HTTP 주소(HTTPS 주소 아님)를 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="07ea8-189">**새로 고침**을 선택하여 **사용 가능한 프로세스**에 있는 항목을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="07ea8-190">디버그할 브라우저 프로세스를 선택하고 **연결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="07ea8-191">**코드 형식 선택** 대화 상자에서 연결할 특정 브라우저(Edge 또는 Chrome)의 코드 형식을 선택하고 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="07ea8-192">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="07ea8-192">Browser source maps</span></span>

<span data-ttu-id="07ea8-193">브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="07ea8-194">그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="07ea8-195">대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="07ea8-196">문제 해결</span><span class="sxs-lookup"><span data-stu-id="07ea8-196">Troubleshoot</span></span>

<span data-ttu-id="07ea8-197">오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="07ea8-198">**디버거** 탭에서 브라우저의 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="07ea8-199">콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="07ea8-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
