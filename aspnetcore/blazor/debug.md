---
title: 디버그 ASP.NET Core Blazor
author: guardrex
description: Blazor apps를 디버그 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/31/2019
uid: blazor/debug
ms.openlocfilehash: 37c6009727a4f62b61793adca0d83cdd53be4b9a
ms.sourcegitcommit: 3204bc89ae6354b61ee0a9b2770ebe5214b7790c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68948373"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="a5bcf-103">디버그 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a5bcf-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="a5bcf-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="a5bcf-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="a5bcf-105">*조기* 지원은 Blazor에서 실행 되는 클라이언트 쪽 앱을 디버그 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-105">*Early* support exists for debugging Blazor client-side apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="a5bcf-106">디버거 기능이 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="a5bcf-107">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-107">Available scenarios include:</span></span>

* <span data-ttu-id="a5bcf-108">중단점을 설정 하 고 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="a5bcf-109">코드 또는 resume (`F10``F8`) 코드 실행을 통해 단일 단계 ()를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="a5bcf-110">*지역* 디스플레이에서, `int` `string`및 `bool`형식의 지역 변수 값을 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="a5bcf-111">JavaScript에서 .NET으로, .NET에서 JavaScript로 이동 하는 호출 체인을 포함 하 여 호출 스택을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="a5bcf-112">다음을 할 *수 없습니다*.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-112">You *can't*:</span></span>

* <span data-ttu-id="a5bcf-113">`int` ,`string`또는 가`bool`아닌 모든 지역 값을 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="a5bcf-114">모든 클래스 속성 또는 필드의 값을 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="a5bcf-115">변수를 마우스로 가리켜 해당 값을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="a5bcf-116">콘솔에서 식을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="a5bcf-117">비동기 호출을 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-117">Step across async calls.</span></span>
* <span data-ttu-id="a5bcf-118">대부분의 다른 일반적인 디버깅 시나리오를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="a5bcf-119">추가 디버깅 시나리오를 개발 하는 것은 엔지니어링 팀에 집중 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a5bcf-120">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a5bcf-120">Prerequisites</span></span>

<span data-ttu-id="a5bcf-121">디버깅 하려면 다음 브라우저 중 하나가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-121">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="a5bcf-122">Google Chrome (버전 70 이상)</span><span class="sxs-lookup"><span data-stu-id="a5bcf-122">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="a5bcf-123">Microsoft Edge preview ([Edge Dev 채널](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="a5bcf-123">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="a5bcf-124">프로시저</span><span class="sxs-lookup"><span data-stu-id="a5bcf-124">Procedure</span></span>

1. <span data-ttu-id="a5bcf-125">구성에서 `Debug` Blazor 클라이언트 쪽 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-125">Run a Blazor client-side app in `Debug` configuration.</span></span> <span data-ttu-id="a5bcf-126">`--configuration Debug`옵션을 [dotnetrun](/dotnet/core/tools/dotnet-run) 명령`dotnet run --configuration Debug`에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-126">Pass the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="a5bcf-127">브라우저에서 앱에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-127">Access the app in the browser.</span></span>
1. <span data-ttu-id="a5bcf-128">개발자 도구 패널이 아닌 앱에 키보드 포커스를 둡니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-128">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="a5bcf-129">디버깅을 시작할 때 개발자 도구 패널을 닫을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-129">The developer tools panel can be closed when debugging is initiated.</span></span>
1. <span data-ttu-id="a5bcf-130">다음 Blazor 바로 가기 키를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-130">Select the following Blazor-specific keyboard shortcut:</span></span>
   * <span data-ttu-id="a5bcf-131">`Shift+Alt+D`Windows/Linux에서</span><span class="sxs-lookup"><span data-stu-id="a5bcf-131">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="a5bcf-132">`Shift+Cmd+D`macOS</span><span class="sxs-lookup"><span data-stu-id="a5bcf-132">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="a5bcf-133">원격 디버깅 사용</span><span class="sxs-lookup"><span data-stu-id="a5bcf-133">Enable remote debugging</span></span>

<span data-ttu-id="a5bcf-134">원격 디버깅을 사용 하지 않도록 설정 하면 Chrome에서 디버깅 가능한 브라우저 탭 오류 페이지를 **찾을 수 없습니다** . 페이지가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-134">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="a5bcf-135">오류 페이지에는 Blazor 디버깅 프록시가 앱에 연결할 수 있도록 디버깅 포트가 열려 있는 상태로 Chrome을 실행 하기 위한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-135">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="a5bcf-136">*모든 chrome 인스턴스를 닫고* 설명 대로 chrome을 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-136">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="a5bcf-137">앱 디버그</span><span class="sxs-lookup"><span data-stu-id="a5bcf-137">Debug the app</span></span>

<span data-ttu-id="a5bcf-138">원격 디버깅을 사용 하도록 설정 된 상태에서 Chrome을 실행 하면 디버깅 바로 가기 키가 새 디버거 탭을 엽니다. 잠시 후에 **소스** 탭에는 앱의 .net 어셈블리 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-138">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="a5bcf-139">각 어셈블리를 확장 하 고 디버깅에 사용할 수 있는 *.cs*/*razor* 소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-139">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="a5bcf-140">중단점을 설정 하 고, 앱의 탭으로 다시 전환 하 고, 코드가 실행 될 때 중단점이 적중 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-140">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="a5bcf-141">중단점이 적중 되 면 코드 또는 resume (`F10``F8`) 코드를 통해 단일 단계 ()로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-141">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

<span data-ttu-id="a5bcf-142">Blazor는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/) 을 구현 하 고를 사용 하 여 프로토콜을 보강 하는 디버깅 프록시를 제공 합니다. 네트워크 관련 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-142">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="a5bcf-143">디버그 바로 가기 키를 누르면 Blazor는 프록시의 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-143">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="a5bcf-144">프록시는 디버깅 하려는 브라우저 창에 연결 됩니다. 따라서 원격 디버깅을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-144">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="a5bcf-145">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="a5bcf-145">Browser source maps</span></span>

<span data-ttu-id="a5bcf-146">브라우저 소스 맵을 사용 하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑하고 클라이언트 쪽 디버깅에 일반적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-146">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="a5bcf-147">그러나 Blazor는 현재 JavaScript/ C# WASM에 직접 매핑되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-147">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="a5bcf-148">대신, Blazor는 브라우저 내에서 IL 해석을 수행 하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-148">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="a5bcf-149">문제 해결 팁</span><span class="sxs-lookup"><span data-stu-id="a5bcf-149">Troubleshooting tip</span></span>

<span data-ttu-id="a5bcf-150">오류가 발생 하는 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-150">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="a5bcf-151">**디버거** 탭의 브라우저에서 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-151">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="a5bcf-152">콘솔에서를 실행 `localStorage.clear()` 하 여 모든 중단점을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5bcf-152">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
