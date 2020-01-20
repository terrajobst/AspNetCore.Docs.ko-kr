---
title: 디버그 ASP.NET Core Blazor
author: guardrex
description: Blazor 앱을 디버그 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159991"
---
# <a name="debug-aspnet-core-opno-locblazor"></a><span data-ttu-id="4d2a4-103">디버그 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4d2a4-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="4d2a4-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="4d2a4-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4d2a4-105">Chromium 기반 브라우저의 브라우저 개발 도구 (Chrome/Edge)를 사용 하 여 Blazor Weasembmbs 디버깅을 위한 *초기* 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-105">*Early* support exists for debugging Blazor WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="4d2a4-106">작업이 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-106">Work is in progress to:</span></span>

* <span data-ttu-id="4d2a4-107">Visual Studio에서 디버깅을 완전히 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="4d2a4-108">Visual Studio Code에서 디버깅을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="4d2a4-109">디버거 기능이 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="4d2a4-110">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-110">Available scenarios include:</span></span>

* <span data-ttu-id="4d2a4-111">중단점을 설정 하 고 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="4d2a4-112">코드 또는 resume (`F8`) 코드 실행을 통해 단일 단계 (`F10`)를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="4d2a4-113">*지역* 표시에서 `int`, `string`및 `bool`형식의 지역 변수 값을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="4d2a4-114">JavaScript에서 .NET으로, .NET에서 JavaScript로 이동 하는 호출 체인을 포함 하 여 호출 스택을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="4d2a4-115">다음을 할 *수 없습니다*.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-115">You *can't*:</span></span>

* <span data-ttu-id="4d2a4-116">`int`, `string`또는 `bool`아닌 모든 지역에 대 한 값을 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="4d2a4-117">모든 클래스 속성 또는 필드의 값을 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="4d2a4-118">변수를 마우스로 가리켜 해당 값을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="4d2a4-119">콘솔에서 식을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="4d2a4-120">비동기 호출을 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-120">Step across async calls.</span></span>
* <span data-ttu-id="4d2a4-121">대부분의 다른 일반적인 디버깅 시나리오를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="4d2a4-122">추가 디버깅 시나리오를 개발 하는 것은 엔지니어링 팀에 집중 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4d2a4-123">전제 조건</span><span class="sxs-lookup"><span data-stu-id="4d2a4-123">Prerequisites</span></span>

<span data-ttu-id="4d2a4-124">디버깅 하려면 다음 브라우저 중 하나가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="4d2a4-125">Google Chrome (버전 70 이상)</span><span class="sxs-lookup"><span data-stu-id="4d2a4-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="4d2a4-126">Microsoft Edge preview ([Edge Dev 채널](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="4d2a4-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="4d2a4-127">프로시저</span><span class="sxs-lookup"><span data-stu-id="4d2a4-127">Procedure</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d2a4-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d2a4-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="4d2a4-129">Visual Studio의 디버깅은 초기 개발 단계에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="4d2a4-130">**F5** 디버깅은 현재 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="4d2a4-131">디버깅 하지 않고 `Debug` 구성에서 Blazor Weasembomambomsembomapp**를 실행 합니다 (** **f5**대신 **f5+f5** ).</span><span class="sxs-lookup"><span data-stu-id="4d2a4-131">Run a Blazor WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="4d2a4-132">응용 프로그램의 디버그 속성 ( **디버그** 메뉴의 마지막 항목)을 열고 HTTP **앱 URL**을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="4d2a4-133">Chromium 기반 브라우저 (Edge 베타 또는 Chrome)를 사용 하 여 앱의 HTTP 주소 (HTTPS 주소가 아님)로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="4d2a4-134">브라우저 창에서 앱에 키보드 포커스를 둡니다. 개발자 도구 패널은 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="4d2a4-135">이 절차에 대해서는 개발자 도구 패널을 닫은 상태로 유지 하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="4d2a4-136">디버깅이 시작 된 후 개발자 도구 패널을 다시 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="4d2a4-137">다음 Blazor관련 바로 가기 키를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-137">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="4d2a4-138">Windows의 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="4d2a4-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="4d2a4-139">macOS에서 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="4d2a4-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="4d2a4-140">**디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시 되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="4d2a4-141">원격 디버깅을 사용 하도록 설정한 후:</span><span class="sxs-lookup"><span data-stu-id="4d2a4-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="4d2a4-142">1 \.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-142">1\.</span></span> <span data-ttu-id="4d2a4-143">새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-143">A new browser window opens.</span></span> <span data-ttu-id="4d2a4-144">이전 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-144">Close the prior window.</span></span>

   <span data-ttu-id="4d2a4-145">2 \.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-145">2\.</span></span> <span data-ttu-id="4d2a4-146">브라우저 창에서 앱에 키보드 포커스를 놓습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="4d2a4-147">3\.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-147">3\.</span></span> <span data-ttu-id="4d2a4-148">새 브라우저 창에서 Blazor관련 바로 가기 키를 선택 합니다. Windows에서 `Shift+Alt+D` 또는 macOS에서 `Shift+Cmd+D` 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-148">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="4d2a4-149">4 \.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-149">4\.</span></span> <span data-ttu-id="4d2a4-150">**Devtools** 탭이 브라우저에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="4d2a4-151">**브라우저 창에서 앱의 탭을 다시 선택 합니다.**</span><span class="sxs-lookup"><span data-stu-id="4d2a4-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="4d2a4-152">앱을 Visual Studio에 연결 하려면 [Visual studio에서 프로세스에 연결](#attach-to-process-in-visual-studio) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4d2a4-153">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4d2a4-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4d2a4-154">`--configuration Debug` 옵션을 [dotnet run](/dotnet/core/tools/dotnet-run) 명령에 전달 하 여 `Debug` 구성에서 Blazor Weasembomsembomsembomsemboma 앱을 실행 합니다. `dotnet run --configuration Debug`.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-154">Run a Blazor WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="4d2a4-155">셸의 창에 표시 된 HTTP URL에서 앱으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="4d2a4-156">개발자 도구 패널이 아닌 앱에 키보드 포커스를 둡니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="4d2a4-157">이 절차에 대해서는 개발자 도구 패널을 닫은 상태로 유지 하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="4d2a4-158">디버깅이 시작 된 후 개발자 도구 패널을 다시 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="4d2a4-159">다음 Blazor관련 바로 가기 키를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-159">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="4d2a4-160">Windows의 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="4d2a4-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="4d2a4-161">macOS에서 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="4d2a4-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="4d2a4-162">**디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시 되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="4d2a4-163">원격 디버깅을 사용 하도록 설정한 후:</span><span class="sxs-lookup"><span data-stu-id="4d2a4-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="4d2a4-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-164">1\.</span></span> <span data-ttu-id="4d2a4-165">새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-165">A new browser window opens.</span></span> <span data-ttu-id="4d2a4-166">이전 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-166">Close the prior window.</span></span>

   <span data-ttu-id="4d2a4-167">2 \.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-167">2\.</span></span> <span data-ttu-id="4d2a4-168">브라우저 창에서 앱에 키보드 포커스를 둡니다. 개발자 도구 패널은 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="4d2a4-169">3\.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-169">3\.</span></span> <span data-ttu-id="4d2a4-170">새 브라우저 창에서 Blazor관련 바로 가기 키를 선택 합니다. Windows에서 `Shift+Alt+D` 또는 macOS에서 `Shift+Cmd+D` 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-170">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="4d2a4-171">원격 디버깅 사용</span><span class="sxs-lookup"><span data-stu-id="4d2a4-171">Enable remote debugging</span></span>

<span data-ttu-id="4d2a4-172">원격 디버깅을 사용 하지 않도록 설정 하면 Chrome에서 디버깅 가능한 브라우저 탭 오류 페이지를 **찾을 수 없습니다** . 페이지가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="4d2a4-173">오류 페이지에는 디버깅 포트가 열려 있는 상태에서 Chrome을 실행 하는 지침이 포함 되어 Blazor 디버깅 프록시가 앱에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="4d2a4-174">*모든 chrome 인스턴스를 닫고* 설명 대로 chrome을 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="4d2a4-175">앱 디버그</span><span class="sxs-lookup"><span data-stu-id="4d2a4-175">Debug the app</span></span>

<span data-ttu-id="4d2a4-176">원격 디버깅을 사용 하도록 설정 된 상태에서 Chrome을 실행 하면 디버깅 바로 가기 키가 새 디버거 탭을 엽니다. 잠시 후에 **소스** 탭에는 앱의 .net 어셈블리 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="4d2a4-177">각 어셈블리를 확장 하 고 디버깅에 사용할 수 있는 *.cs*/*razor* 소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="4d2a4-178">중단점을 설정 하 고, 앱의 탭으로 다시 전환 하 고, 코드가 실행 될 때 중단점이 적중 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="4d2a4-179">중단점이 적중 되 면 코드 또는 다시 시작 (`F8`) 코드를 통해 단일 단계 (`F10`)가 정상적으로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="4d2a4-180">는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/) 을 구현 하 고를 사용 하 여 프로토콜을 보강 하는 디버깅 프록시를 제공 합니다. 네트워크 관련 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="4d2a4-181">디버그 바로 가기 키를 누르면 Blazor는 프록시의 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="4d2a4-182">프록시는 디버깅 하려는 브라우저 창에 연결 됩니다. 따라서 원격 디버깅을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="4d2a4-183">Visual Studio에서 프로세스에 연결</span><span class="sxs-lookup"><span data-stu-id="4d2a4-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="4d2a4-184">Visual Studio에서 앱 프로세스에 연결 하는 것은 **F5** 디버깅이 개발 중인 동안 Blazor Weasembmba에 대 한 *임시* 디버깅 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="4d2a4-185">실행 중인 응용 프로그램의 프로세스를 Visual Studio에 연결 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="4d2a4-186">Visual Studio에서 **디버그** > **프로세스에 연결을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="4d2a4-187">**연결 형식**에 대해 **Chrome devtools 프로토콜 websocket (인증 안 함)** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="4d2a4-188">**연결 대상**에 대해 앱의 HTTP 주소 (HTTPS 주소가 아님)를 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="4d2a4-189">**사용 가능한 프로세스**에서 항목을 새로 고치려면 **새로 고침** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="4d2a4-190">디버그할 브라우저 프로세스를 선택 하 고 **연결**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="4d2a4-191">**코드 형식 선택** 대화 상자에서 연결 하는 특정 브라우저의 코드 형식 (가장자리 또는 Chrome)을 선택 하 고 **확인**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="4d2a4-192">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="4d2a4-192">Browser source maps</span></span>

<span data-ttu-id="4d2a4-193">브라우저 소스 맵을 사용 하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑하고 클라이언트 쪽 디버깅에 일반적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="4d2a4-194">그러나 Blazor는 현재 JavaScript/ C# WASM에 직접 매핑되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="4d2a4-195">대신, Blazor는 브라우저 내에서 IL 해석을 수행 하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4d2a4-196">문제 해결</span><span class="sxs-lookup"><span data-stu-id="4d2a4-196">Troubleshoot</span></span>

<span data-ttu-id="4d2a4-197">오류가 발생 하는 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="4d2a4-198">**디버거** 탭의 브라우저에서 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="4d2a4-199">콘솔에서 `localStorage.clear()`를 실행 하 여 모든 중단점을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d2a4-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
