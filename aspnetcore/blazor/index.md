---
title: ASP.NET Core의 Blazor 소개
author: guardrex
description: ASP.NET Core 앱에서 .NET을 사용하여 대화형 클라이언트 쪽 웹 UI를 빌드하는 방법인 ASP.NET Core Blazor를 살펴봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/18/2019
uid: blazor/index
ms.openlocfilehash: 74eeb003c249fc9ff8267ac855455f875806ccd9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982999"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="10faa-103">Blazor 소개</span><span class="sxs-lookup"><span data-stu-id="10faa-103">Introduction to Blazor</span></span>

<span data-ttu-id="10faa-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10faa-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="10faa-105">Blazor를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-105">Welcome to Blazor!</span></span>

<span data-ttu-id="10faa-106">.NET을 사용하여 대화형 클라이언트 쪽 웹 UI 빌드:</span><span class="sxs-lookup"><span data-stu-id="10faa-106">Build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="10faa-107">JavaScript 대신 C#을 사용하여 풍부한 대화형 UI를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="10faa-108">.NET으로 작성된 서버 쪽 및 클라이언트 쪽 앱 논리를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-108">Share server-side and client-side app logic written with .NET.</span></span>
* <span data-ttu-id="10faa-109">모바일 브라우저를 포함한 광범위한 브라우저 지원을 위해 UI를 HTML 및 CSS로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="10faa-110">Blazor는 대부분의 앱에 필요한 다음과 같은 핵심 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-110">Blazor supports core scenarios required by most apps:</span></span>

* <span data-ttu-id="10faa-111">매개 변수</span><span class="sxs-lookup"><span data-stu-id="10faa-111">Parameters</span></span>
* <span data-ttu-id="10faa-112">이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="10faa-112">Event handling</span></span>
* <span data-ttu-id="10faa-113">데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="10faa-113">Data binding</span></span>
* <span data-ttu-id="10faa-114">라우팅</span><span class="sxs-lookup"><span data-stu-id="10faa-114">Routing</span></span>
* <span data-ttu-id="10faa-115">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="10faa-115">Dependency injection</span></span>
* <span data-ttu-id="10faa-116">레이아웃</span><span class="sxs-lookup"><span data-stu-id="10faa-116">Layouts</span></span>
* <span data-ttu-id="10faa-117">템플릿</span><span class="sxs-lookup"><span data-stu-id="10faa-117">Templates</span></span>
* <span data-ttu-id="10faa-118">연계 값</span><span class="sxs-lookup"><span data-stu-id="10faa-118">Cascading values</span></span>

## <a name="components"></a><span data-ttu-id="10faa-119">구성 요소</span><span class="sxs-lookup"><span data-stu-id="10faa-119">Components</span></span>

<span data-ttu-id="10faa-120">Blazor의 *구성 요소*는 페이지, 대화 상자 또는 데이터 입력 양식과 같은 UI의 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-120">A *component* in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="10faa-121">구성 요소는 사용자 이벤트를 처리하고 유연한 UI 렌더링 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-121">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="10faa-122">구성 요소는 중첩 및 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-122">Components can be nested and reused.</span></span>

<span data-ttu-id="10faa-123">구성 요소는 NuGet 패키지로 공유 및 배포될 수 있는 .NET 어셈블리에 기본 제공된 .NET 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-123">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="10faa-124">구성 요소 클래스는 일반적으로 *.razor* 파일 확장자를 가진 Razor 태그 페이지 형식으로 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-124">The component class is usually written in the form of a Razor markup page with a *.razor* file extension.</span></span> <span data-ttu-id="10faa-125">Blazor의 구성 요소는 경우에 따라 Razor 구성 요소라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-125">Components in Blazor are sometimes referred to as Razor components.</span></span>

<span data-ttu-id="10faa-126">[Razor](xref:mvc/views/razor)는 HTML 태그와 C# 코드를 결합하는 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-126">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="10faa-127">Razor는 개발자 생산성을 위해 설계되었으며, 개발자는 [IntelliSense](/visualstudio/ide/using-intellisense) 지원으로 동일한 파일에서 태그와 C# 사이를 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-127">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="10faa-128">Razor Pages 및 MVC 뷰에도 Razor를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-128">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="10faa-129">요청/응답 모델을 중심으로 빌드된 Razor Pages 및 MVC 뷰와는 달리, 구성 요소는 특별히 UI 컴퍼지션을 처리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-129">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="10faa-130">Razor 구성 요소는 클라이언트 쪽 UI 논리 및 컴퍼지션에 특별히 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-130">Razor components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="10faa-131">다음 태그는 사용자 지정 대화 상자 구성 요소의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-131">The following markup is an example of a custom dialog component:</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="10faa-132">이 구성 요소를 앱의 다른 곳에서 사용할 경우 [Visual Studio](https://visualstudio.microsoft.com/vs/)의 IntelliSense는 구문 및 매개 변수 완성을 통해 개발 속도를 높입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-132">When this component is used elsewhere in the app, IntelliSense in [Visual Studio](https://visualstudio.microsoft.com/vs/) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="10faa-133">구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용할 수 있는 ‘렌더링 트리’라는 브라우저 DOM의 메모리 내 표시로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-133">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-server-side"></a><span data-ttu-id="10faa-134">Blazor 서버 쪽</span><span class="sxs-lookup"><span data-stu-id="10faa-134">Blazor server-side</span></span>

<span data-ttu-id="10faa-135">Razor는 UI 업데이트 적용 방법에서 구성 요소 렌더링 논리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-135">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="10faa-136">Blazor 서버 쪽에서는 ASP.NET Core 앱의 서버에서 Razor 구성 요소를 호스팅할 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-136">Blazor server-side provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="10faa-137">UI 업데이트는 SignalR 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-137">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="10faa-138">런타임이 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-138">The runtime:</span></span>

* <span data-ttu-id="10faa-139">브라우저에서 서버로 UI 이벤트 보내기를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-139">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="10faa-140">구성 요소를 실행한 후 서버가 보낸 UI 업데이트를 다시 브라우저에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-140">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="10faa-141">Blazor 서버 쪽에서 브라우저와 통신하기 위해 사용하는 연결은 JavaScript interop 호출을 처리하는 데도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-141">The connection used by Blazor server-side to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor 서버 쪽에서 서버의 .NET 코드를 실행하고 SignalR 연결을 통해 클라이언트의 문서 개체 모델과 상호 작용합니다.](index/_static/blazor-server-side.png)

<span data-ttu-id="10faa-143">자세한 내용은 <xref:blazor/hosting-models#server-side>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="10faa-143">For more information, see <xref:blazor/hosting-models#server-side>.</span></span>

## <a name="blazor-client-side"></a><span data-ttu-id="10faa-144">Blazor 클라이언트 쪽</span><span class="sxs-lookup"><span data-stu-id="10faa-144">Blazor client-side</span></span>

<span data-ttu-id="10faa-145">Blazor 클라이언트 쪽은 .NET을 사용하여 대화형 클라이언트 쪽 웹앱을 빌드하기 위한 단일 페이지 앱 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-145">Blazor client-side is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="10faa-146">Blazor 클라이언트 쪽은 플러그 인이나 코드 소스 간 컴파일 없이 개방형 웹 표준을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-146">Blazor client-side uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="10faa-147">Blazor 클라이언트 쪽은 모바일 브라우저를 비롯한 모든 최신 웹 브라우저에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-147">Blazor client-side works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="10faa-148">브라우저에서 클라이언트 쪽 웹 개발에 .NET을 사용하면 다음과 같은 많은 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-148">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="10faa-149">**C# 언어**: JavaScript 대신 C#으로 코드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-149">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="10faa-150">**.NET 에코시스템**: .NET 라이브러리의 기존 에코시스템을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-150">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="10faa-151">**전체 스택 개발**: 서버 및 클라이언트 쪽 논리를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-151">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="10faa-152">**속도 및 확장성**: .NET은 성능, 안정성 및 보안을 위해 빌드되었습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-152">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="10faa-153">**업계 최고의 도구**: Windows, Linux 및 macOS에서 Visual Studio를 사용하여 생산성을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-153">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="10faa-154">**안정성 및 일관성**:  안정적이고, 기능이 풍부하고, 사용하기 쉬운 공통 언어, 프레임워크 및 도구 세트를 기반으로 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-154">**Stability and consistency**:  Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="10faa-155">웹 브라우저 내에서 .NET 코드를 실행하는 것은 [WebAssembly](http://webassembly.org)(약식 *wasm*)를 통해 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-155">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="10faa-156">WebAssembly는 개방형 웹 표준이고 플러그 인 없이 웹 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-156">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="10faa-157">WebAssembly는 빠른 다운로드와 최대 실행 속도를 위해 최적화된 압축 바이트 코드 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-157">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="10faa-158">WebAssembly 코드는 JavaScript interop을 통해 브라우저의 전체 기능에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-158">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="10faa-159">동시에, WebAssembly를 통해 실행되는 .NET 코드는 JavaScript와 동일한 신뢰할 수 있는 샌드박스에서 실행되어 클라이언트 머신에서 악의적인 동작을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-159">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor 클라이언트 쪽에서는 WebAssembly와 함께 브라우저에서 .NET 코드를 실행합니다.](index/_static/blazor-client-side.png)

<span data-ttu-id="10faa-161">Blazor 클라이언트 쪽 앱이 빌드되고 브라우저에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="10faa-161">When a Blazor client-side app is built and run in a browser:</span></span>

* <span data-ttu-id="10faa-162">C# 코드 파일과 Razor 파일은 .NET 어셈블리로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-162">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="10faa-163">어셈블리와 .NET 런타임이 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-163">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="10faa-164">Blazor 클라이언트 쪽에서 .NET 런타임을 부트스트랩하고 앱의 어셈블리를 로드하도록 런타임을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-164">Blazor client-side bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="10faa-165">DOM(문서 개체 모델) 조작 및 브라우저 API 호출은 JavaScript interop을 통해 Blazor 클라이언트 쪽 런타임에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-165">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor client-side runtime via JavaScript interop.</span></span>

<span data-ttu-id="10faa-166">다운로드된 앱의 크기를 줄이기 위해 [IL(중간 언어) 링커](xref:host-and-deploy/blazor/configure-linker)에서 게시하면 사용되지 않는 코드가 앱에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-166">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="10faa-167">Blazor 클라이언트 쪽은 클라이언트 쪽 호스팅 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-167">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="10faa-168">Blazor는 UI 업데이트 적용 방식에서 구성 요소의 렌더링 논리를 분리하기 때문에 Blazor를 호스트하는 방법에 유연성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-168">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="10faa-169">[Blazor 서버 쪽](#blazor-server-side)을 사용하여 SignalR 연결을 통해 UI 업데이트를 처리하는 ASP.NET Core 앱의 서버에서 Blazor를 호스팅합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-169">Use [Blazor server-side](#blazor-server-side) to host Blazor on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="10faa-170">자세한 내용은 <xref:blazor/hosting-models#server-side>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="10faa-170">For more information, see <xref:blazor/hosting-models#server-side>.</span></span> 

<span data-ttu-id="10faa-171">페이로드 크기는 앱의 유용성에 중요한 성능 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-171">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="10faa-172">Blazor 클라이언트 쪽에서는 페이로드 크기를 최적화하여 다운로드 시간을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-172">Blazor client-side optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="10faa-173">.NET 어셈블리의 사용되지 않는 부분은 빌드 프로세스 중에 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-173">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="10faa-174">HTTP 응답이 압축됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-174">HTTP responses are compressed.</span></span>
* <span data-ttu-id="10faa-175">.NET 런타임 및 어셈블리가 브라우저에 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-175">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="10faa-176">[Razor 서버 쪽](#blazor-server-side)은 .NET 어셈블리, 앱의 어셈블리 및 런타임 서버 쪽을 유지 관리하여 Blazor 클라이언트 쪽보다 작은 페이로드 크기를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-176">[Blazor server-side](#blazor-server-side) provides a smaller payload size than Blazor client-side by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="10faa-177">Blazor 서버 쪽 앱은 클라이언트에 태그 파일 및 정적 자산만 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-177">Blazor server-side apps only serve markup files and static assets to clients.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="10faa-178">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="10faa-178">JavaScript interop</span></span>

<span data-ttu-id="10faa-179">타사 JavaScript 라이브러리 및 브라우저 API를 필요로 하는 앱의 경우 구성 요소는 JavaScript와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-179">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="10faa-180">구성 요소는 JavaScript가 사용할 수 있는 모든 라이브러리 또는 API를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="10faa-181">C# 코드는 JavaScript 코드로 호출할 수 있으며, JavaScript 코드는 C# 코드로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-181">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="10faa-182">자세한 내용은 [JavaScript interop](xref:blazor/javascript-interop)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="10faa-182">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="10faa-183">코드 공유 및 .NET Standard</span><span class="sxs-lookup"><span data-stu-id="10faa-183">Code sharing and .NET Standard</span></span>

<span data-ttu-id="10faa-184">앱은 기존 [.NET Standard](/dotnet/standard/net-standard) 라이브러리를 참조하고 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-184">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="10faa-185">.NET Standard는 .NET 구현에서 공통적인 .NET API의 공식 사양입니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-185">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="10faa-186">Blazor는 .NET Standard 2.0을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-186">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="10faa-187">웹 브라우저 내에서 적용되지 않는 API(예: 파일 시스템 액세스, 소켓 열기, 스레딩 및 기타 기능)에서 <xref:System.PlatformNotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-187">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="10faa-188">.NET Standard 클래스 라이브러리는 Blazor, .NET Framework, .NET Core, Xamarin, Mono, Unity 등 다양한 .NET 플랫폼 간에 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10faa-188">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10faa-189">추가 자료</span><span class="sxs-lookup"><span data-stu-id="10faa-189">Additional resources</span></span>

* [<span data-ttu-id="10faa-190">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="10faa-190">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="10faa-191">C# 가이드</span><span class="sxs-lookup"><span data-stu-id="10faa-191">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="10faa-192">HTML</span><span class="sxs-lookup"><span data-stu-id="10faa-192">HTML</span></span>](https://www.w3.org/html/)
