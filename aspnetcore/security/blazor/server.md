---
title: Blazor Server 앱 보안 ASP.NET Core
author: guardrex
description: Blazor Server 앱에 대 한 보안 위협을 완화 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: d87aac02137681e62cf8f5cbd4dc8b0be6f8431e
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146305"
---
# <a name="secure-aspnet-core-opno-locblazor-server-apps"></a><span data-ttu-id="b252c-103">Blazor Server 앱 보안 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b252c-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b252c-104">[Javier Calvarro e](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="b252c-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

Blazor<span data-ttu-id="b252c-105"> 서버 앱은 서버와 클라이언트에서 수명이 긴 관계를 유지 하는 *상태 저장* 데이터 처리 모델을 채택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-105"> Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="b252c-106">영구 상태는 잠재적으로 수명이 긴 연결에 걸쳐 있을 수 있는 [회로](xref:blazor/state-management)에 의해 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="b252c-107">사용자가 Blazor 서버 사이트를 방문 하면 서버는 서버 메모리에 회로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="b252c-108">회로는 사용자가 UI에서 단추를 선택 하는 경우와 같이 렌더링할 콘텐츠를 브라우저에 표시 하 고 이벤트에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="b252c-109">이러한 작업을 수행 하기 위해 회로는 사용자의 브라우저에서 JavaScript 함수를 호출 하 고 서버에서 .NET 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="b252c-110">이 양방향 JavaScript 기반 상호 작용을 [javascript interop (JS interop)](xref:blazor/javascript-interop)라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/javascript-interop).</span></span>

<span data-ttu-id="b252c-111">JS interop는 인터넷을 통해 발생 하 고 클라이언트는 원격 브라우저를 사용 하기 때문에 대부분의 웹 앱 보안 문제를 공유 하는 Blazor 서버 응용 프로그램입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="b252c-112">이 항목에서는 Blazor Server 앱에 대 한 일반적인 위협에 대해 설명 하 고 인터넷 연결 앱에 초점을 맞춘 위협 완화 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="b252c-113">회사 네트워크 또는 인트라넷 내부와 같은 제한 된 환경에서 일부 완화 지침은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="b252c-114">제약 조건 환경에는 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="b252c-115">제한 된 환경에서는 보안 위험이 낮으므로 구현 비용이 들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="resource-exhaustion"></a><span data-ttu-id="b252c-116">리소스 소모</span><span class="sxs-lookup"><span data-stu-id="b252c-116">Resource exhaustion</span></span>

<span data-ttu-id="b252c-117">리소스가 서버와 상호 작용 하 고 서버가 과도 한 리소스를 사용 하는 경우 리소스가 고갈 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-117">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="b252c-118">과도 한 리소스 소비가 주로 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-118">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="b252c-119">CPU</span><span class="sxs-lookup"><span data-stu-id="b252c-119">CPU</span></span>](#cpu)
* [<span data-ttu-id="b252c-120">메모리</span><span class="sxs-lookup"><span data-stu-id="b252c-120">Memory</span></span>](#memory)
* [<span data-ttu-id="b252c-121">클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="b252c-121">Client connections</span></span>](#client-connections)

<span data-ttu-id="b252c-122">DoS (서비스 거부) 공격은 일반적으로 앱 또는 서버 리소스의 고갈를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-122">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="b252c-123">그러나 리소스 소모는 시스템의 공격에 대 한 결과일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-123">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="b252c-124">예를 들어 높은 사용자 수요로 인해 유한 리소스를 모두 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-124">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="b252c-125">DoS는 [dos (서비스 거부) 공격](#denial-of-service-dos-attacks) 섹션에 자세히 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-125">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="b252c-126">데이터베이스 및 파일 핸들 (파일 읽기 및 쓰기에 사용 됨)과 같은 Blazor 프레임 워크 외부 리소스에는 리소스가 고갈 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-126">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="b252c-127">자세한 내용은 <xref:performance/performance-best-practices>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-127">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="b252c-128">CPU</span><span class="sxs-lookup"><span data-stu-id="b252c-128">CPU</span></span>

<span data-ttu-id="b252c-129">하나 이상의 클라이언트에서 서버가 CPU 작업을 많이 수행 하도록 강제 하는 경우 CPU가 고갈 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-129">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="b252c-130">예를 들어 *Fibonnacci 번호*를 계산 하는 Blazor Server 앱이 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-130">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="b252c-131">Fibonnacci number는 Fibonnacci 시퀀스에서 생성 됩니다. 여기서 시퀀스의 각 숫자는 위의 두 숫자의 합계입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-131">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="b252c-132">응답에 도달 하는 데 필요한 작업량은 시퀀스의 길이와 초기 값의 크기에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-132">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="b252c-133">앱이 클라이언트의 요청에 제한을 두지 않으면 CPU를 많이 사용 하는 계산이 CPU의 시간을 차지 하 고 다른 작업의 성능을 저하 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-133">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="b252c-134">과도 한 리소스 소비는 가용성에 영향을 주는 보안 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-134">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="b252c-135">CPU 소모는 모든 공용 앱에서 중요 한 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-135">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="b252c-136">일반 웹 앱에서 요청 및 연결은 보호로 제한 되지만 Blazor Server 앱은 동일한 보호 기능을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-136">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> Blazor<span data-ttu-id="b252c-137"> 서버 앱은 CPU 사용량이 많은 작업을 수행 하기 전에 적절 한 검사와 제한을 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-137"> Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="b252c-138">메모리</span><span class="sxs-lookup"><span data-stu-id="b252c-138">Memory</span></span>

<span data-ttu-id="b252c-139">하나 이상의 클라이언트가 서버에서 많은 양의 메모리를 강제로 사용 하는 경우 메모리 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-139">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="b252c-140">예를 들어 항목 목록을 수락 하 고 표시 하는 구성 요소가 포함 된 Blazor서버 쪽 앱을 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-140">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="b252c-141">Blazor 앱에서 허용 되는 항목 수 또는 클라이언트로 다시 렌더링 되는 항목 수에 대 한 제한을 설정 하지 않으면 메모리를 많이 사용 하는 처리 및 렌더링이 서버의 메모리를 저하 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-141">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="b252c-142">서버 작동이 중단 된 것으로 표시 되 면 서버 작동이 중단 되거나 속도가 느려질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-142">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="b252c-143">서버의 잠재적 메모리 소모 시나리오와 관련 된 항목의 목록을 유지 관리 하 고 표시 하려면 다음 시나리오를 고려 하십시오.</span><span class="sxs-lookup"><span data-stu-id="b252c-143">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="b252c-144">`List<MyItem>` 속성 또는 필드의 항목은 서버의 메모리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-144">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="b252c-145">앱에서 항목 목록을 무제한으로 확장할 수 있는 경우 서버의 메모리가 부족 해질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-145">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="b252c-146">메모리가 부족 하면 현재 세션이 종료 되 고 (충돌) 해당 서버 인스턴스의 모든 동시 세션에서 메모리 부족 예외가 수신 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-146">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="b252c-147">이 시나리오가 발생 하지 않도록 하려면 앱에서 동시 사용자에 게 항목 제한을 적용 하는 데이터 구조를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-147">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="b252c-148">렌더링에 페이징 스키마를 사용 하지 않는 경우 서버는 UI에 표시 되지 않는 개체에 추가 메모리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-148">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="b252c-149">항목 수를 제한 하지 않으면 메모리 요구가 사용 가능한 서버 메모리를 고갈 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-149">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="b252c-150">이 시나리오를 방지 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-150">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="b252c-151">렌더링할 때 페이지가 매겨진 목록을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-151">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="b252c-152">1000 항목에 처음 100 개만 표시 하 고, 표시 된 항목 이외의 항목을 찾기 위해 사용자가 검색 조건을 입력 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-152">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="b252c-153">고급 렌더링 시나리오의 경우 *가상화*를 지 원하는 목록 또는 그리드를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-153">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="b252c-154">가상화를 사용 하면 목록에 현재 사용자에 게 표시 되는 항목의 하위 집합만 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-154">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="b252c-155">사용자가 UI에서 스크롤 막대와 상호 작용할 때 구성 요소는 표시에 필요한 항목만 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-155">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="b252c-156">현재 표시 하는 데 필요 하지 않은 항목은 보조 저장소에 보관 될 수 있습니다 .이는 이상적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-156">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="b252c-157">표시 되지 않은 항목은 메모리에 보관 될 수도 있으므로 그다지 유용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-157">Undisplayed items can also be held in memory, which is less ideal.</span></span>

Blazor<span data-ttu-id="b252c-158"> Server 앱은 WPF, Windows Forms, Blazor Weasembmbambmbambambambamboma와 같은 상태 저장 앱의 다른 UI 프레임 워크에 비슷한 프로그래밍 모델</span><span class="sxs-lookup"><span data-stu-id="b252c-158"> Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="b252c-159">주요 차이점은 몇 가지 UI 프레임 워크에서 응용 프로그램에 사용 되는 메모리는 클라이언트에 속하며 개별 클라이언트에만 영향을 준다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-159">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="b252c-160">예를 들어 Blazor Weasembomapp은 전적으로 클라이언트에서 실행 되며 클라이언트 메모리 리소스만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-160">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="b252c-161">Blazor Server 시나리오에서 앱이 사용 하는 메모리는 서버에 속하며 서버 인스턴스의 클라이언트 간에 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-161">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="b252c-162">서버 쪽 메모리 요구는 모든 Blazor 서버 앱에 대 한 고려 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-162">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="b252c-163">그러나 대부분의 웹 앱은 상태 비저장 이며, 요청 처리 중에 사용 되는 메모리는 응답이 반환 될 때 해제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-163">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="b252c-164">일반적으로 클라이언트 연결을 유지 하는 다른 서버 쪽 앱에서와 같이 클라이언트가 바인딩되지 않은 메모리 양을 할당 하도록 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-164">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="b252c-165">Blazor Server 앱에서 사용 하는 메모리는 단일 요청 보다 오랜 시간 동안 지속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-165">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="b252c-166">개발 하는 동안 프로파일러를 사용 하거나 클라이언트의 메모리 수요를 평가 하기 위해 캡처된 추적을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-166">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="b252c-167">프로파일러 또는 추적은 특정 클라이언트에 할당 된 메모리를 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-167">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="b252c-168">개발 하는 동안 특정 클라이언트의 메모리 사용을 캡처하려면 덤프를 캡처하고 사용자 회로에서 루트 된 모든 개체의 메모리 수요를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-168">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="b252c-169">클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="b252c-169">Client connections</span></span>

<span data-ttu-id="b252c-170">하나 이상의 클라이언트에서 서버에 대 한 동시 연결을 너무 많이 열어 다른 클라이언트가 새 연결을 설정 하지 못하게 되 면 연결이 고갈 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-170">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

Blazor<span data-ttu-id="b252c-171"> 클라이언트는 세션당 단일 연결을 설정 하 고 브라우저 창이 열려 있는 동안에는 연결을 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-171"> clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="b252c-172">모든 연결을 유지 관리 하는 서버에 대 한 요청은 Blazor 앱에 국한 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-172">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="b252c-173">연결의 지속적인 특성과 Blazor Server 앱의 상태 저장 특성을 고려 하 여 연결 고갈는 응용 프로그램의 가용성에 더 많은 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-173">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="b252c-174">기본적으로 Blazor Server 앱에 대 한 사용자 당 연결 수에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-174">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="b252c-175">앱에 연결 제한이 필요한 경우 다음 방법 중 하나 이상을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-175">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="b252c-176">인증을 요구 하 여 인증 되지 않은 사용자가 앱에 연결 하는 기능을 자연스럽 게 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-176">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="b252c-177">이 시나리오를 적용 하려면 사용자가 새 사용자를 프로 비전 할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-177">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="b252c-178">사용자 당 연결 수를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-178">Limit the number of connections per user.</span></span> <span data-ttu-id="b252c-179">다음 방법을 통해 연결 제한을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-179">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="b252c-180">합법적인 사용자가 응용 프로그램에 액세스할 수 있도록 합니다 (예: 클라이언트의 IP 주소를 기준으로 연결 제한이 설정 된 경우).</span><span class="sxs-lookup"><span data-stu-id="b252c-180">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="b252c-181">앱 수준:</span><span class="sxs-lookup"><span data-stu-id="b252c-181">At the app level:</span></span>
    * <span data-ttu-id="b252c-182">끝점 라우팅 확장성.</span><span class="sxs-lookup"><span data-stu-id="b252c-182">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="b252c-183">앱에 연결 하 고 사용자 당 활성 세션을 추적 하려면 인증이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-183">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="b252c-184">한도에 도달 하면 새 세션을 거부 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-184">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="b252c-185">클라이언트에서 앱으로의 연결을 다중 송신 하는 [Azure SignalR 서비스](/azure/azure-signalr/signalr-overview) 와 같은 프록시를 사용 하 여 앱에 대 한 프록시 WebSocket 연결입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-185">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="b252c-186">이를 통해 단일 클라이언트가 설정할 수 있는 것 보다 더 많은 연결 용량이 있는 앱을 제공 하 여 클라이언트가 서버에 대 한 연결을 고갈 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-186">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="b252c-187">서버 수준: 앱 앞에 프록시/게이트웨이를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-187">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="b252c-188">예를 들어 [Azure Front 도어](/azure/frontdoor/front-door-overview) 를 사용 하면 앱에 대 한 웹 트래픽의 글로벌 라우팅을 정의, 관리 및 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-188">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="b252c-189">DoS (서비스 거부) 공격</span><span class="sxs-lookup"><span data-stu-id="b252c-189">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="b252c-190">DoS (서비스 거부) 공격에는 서버에서 하나 이상의 리소스를 소모 하 여 앱을 사용할 수 없게 만드는 클라이언트가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-190">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> Blazor<span data-ttu-id="b252c-191"> Server 앱은 몇 가지 기본 제한을 포함 하 고 다른 ASP.NET Core 및 SignalR 제한을 사용 하 여 DoS 공격 으로부터 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-191"> Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| Blazor<span data-ttu-id="b252c-192"> Server 앱 제한</span><span class="sxs-lookup"><span data-stu-id="b252c-192"> Server app limit</span></span>                            | <span data-ttu-id="b252c-193">설명</span><span class="sxs-lookup"><span data-stu-id="b252c-193">Description</span></span> | <span data-ttu-id="b252c-194">기본</span><span class="sxs-lookup"><span data-stu-id="b252c-194">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="b252c-195">지정 된 서버에서 한 번에 메모리에 보유 하는 연결 되지 않은 최대 회로 수입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-195">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="b252c-196">100</span><span class="sxs-lookup"><span data-stu-id="b252c-196">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="b252c-197">연결이 끊어지기 전에 연결이 끊어진 회로가 메모리에 보관 되는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-197">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="b252c-198">3분</span><span class="sxs-lookup"><span data-stu-id="b252c-198">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="b252c-199">비동기 JavaScript 함수 호출 시간이 초과 될 때까지 서버가 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-199">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="b252c-200">1분</span><span class="sxs-lookup"><span data-stu-id="b252c-200">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="b252c-201">승인 되지 않은 최대 렌더링 일괄 처리 수 서버는 지정 된 시간에 회로 당 메모리에 유지 하 여 강력한 다시 연결을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-201">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="b252c-202">한도에 도달 하면 클라이언트에서 하나 이상의 일괄 처리를 승인할 때까지 서버가 새 렌더링 일괄 처리 생성을 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-202">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="b252c-203">10</span><span class="sxs-lookup"><span data-stu-id="b252c-203">10</span></span> |


| SignalR<span data-ttu-id="b252c-204"> 및 ASP.NET Core 제한</span><span class="sxs-lookup"><span data-stu-id="b252c-204"> and ASP.NET Core limit</span></span>             | <span data-ttu-id="b252c-205">설명</span><span class="sxs-lookup"><span data-stu-id="b252c-205">Description</span></span> | <span data-ttu-id="b252c-206">기본</span><span class="sxs-lookup"><span data-stu-id="b252c-206">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="b252c-207">개별 메시지의 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-207">Message size for an individual message.</span></span> | <span data-ttu-id="b252c-208">32KB</span><span class="sxs-lookup"><span data-stu-id="b252c-208">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="b252c-209">브라우저와의 상호 작용 (클라이언트)</span><span class="sxs-lookup"><span data-stu-id="b252c-209">Interactions with the browser (client)</span></span>

<span data-ttu-id="b252c-210">클라이언트는 JS interop 이벤트 디스패치 및 렌더링 완료를 통해 서버와 상호 작용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-210">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="b252c-211">JS interop 통신은 JavaScript와 .NET 사이에 두 가지 방식으로 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-211">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="b252c-212">브라우저 이벤트는 비동기 방식으로 클라이언트에서 서버로 발송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-212">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="b252c-213">서버는 필요에 따라 UI를 비동기적으로 rerendering 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-213">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="b252c-214">.NET에서 호출 되는 JavaScript 함수</span><span class="sxs-lookup"><span data-stu-id="b252c-214">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="b252c-215">.NET 메서드에서 JavaScript로의 호출:</span><span class="sxs-lookup"><span data-stu-id="b252c-215">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="b252c-216">모든 호출에는 장애 조치 (failover) 후 구성 가능한 시간 제한이 있으므로 호출자에 게 <xref:System.OperationCanceledException>을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-216">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="b252c-217">호출 (`CircuitOptions.JSInteropDefaultCallTimeout`)의 기본 시간 제한은 1 분입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-217">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="b252c-218">이 제한을 구성 하려면 <xref:blazor/javascript-interop#harden-js-interop-calls>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-218">To configure this limit, see <xref:blazor/javascript-interop#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="b252c-219">취소 토큰을 제공 하 여 호출 단위로 취소를 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-219">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="b252c-220">가능 하면 기본 호출 제한 시간을 사용 하 고 취소 토큰이 제공 되는 경우 클라이언트에 대 한 시간 범위를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-220">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="b252c-221">JavaScript 호출 결과를 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-221">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="b252c-222">브라우저에서 실행 중인 Blazor 앱 클라이언트는 호출할 JavaScript 함수를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-222">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="b252c-223">함수가 호출 되 고 결과 또는 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-223">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="b252c-224">악의적인 클라이언트에서 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-224">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="b252c-225">JavaScript 함수에서 오류를 반환 하 여 앱에서 문제를 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-225">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="b252c-226">JavaScript 함수에서 예기치 않은 결과를 반환 하 여 서버에서 의도 하지 않은 동작을 유도 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-226">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="b252c-227">앞의 시나리오를 방지 하기 위해 다음 예방 조치를 취해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-227">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="b252c-228">호출 중에 발생할 수 있는 오류를 고려 하 여 [try-catch 문 내](/dotnet/csharp/language-reference/keywords/try-catch) 에서 JS interop 호출을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-228">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="b252c-229">자세한 내용은 <xref:blazor/handle-errors#javascript-interop>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-229">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="b252c-230">작업을 수행 하기 전에 오류 메시지를 포함 하 여 JS interop 호출에서 반환 된 데이터의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-230">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="b252c-231">브라우저에서 호출 된 .NET 메서드</span><span class="sxs-lookup"><span data-stu-id="b252c-231">.NET methods invoked from the browser</span></span>

<span data-ttu-id="b252c-232">JavaScript에서 .NET 메서드로의 호출을 신뢰 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-232">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="b252c-233">.NET 메서드가 JavaScript에 노출 되는 경우 .NET 메서드를 호출 하는 방법을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-233">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="b252c-234">응용 프로그램에 대 한 공용 끝점과 같이 JavaScript에 노출 된 모든 .NET 메서드를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-234">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="b252c-235">입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-235">Validate input.</span></span>
    * <span data-ttu-id="b252c-236">값이 예상 범위 내에 있는지 확인 하십시오.</span><span class="sxs-lookup"><span data-stu-id="b252c-236">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="b252c-237">사용자에 게 요청 된 작업을 수행할 수 있는 권한이 있는지 확인 하십시오.</span><span class="sxs-lookup"><span data-stu-id="b252c-237">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="b252c-238">.NET 메서드 호출의 일부로 과도 한 리소스를 할당 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-238">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="b252c-239">예를 들어 검사를 수행 하 고 CPU 및 메모리 사용에 대 한 제한을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-239">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="b252c-240">정적 및 인스턴스 메서드를 JavaScript 클라이언트에 노출할 수 있는 경우를 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-240">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="b252c-241">디자인에서 적절 한 제약 조건을 사용 하 여 상태를 공유 하도록 호출 하지 않는 한 세션 간에 상태를 공유 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-241">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="b252c-242">DI (종속성 주입)를 통해 원래 생성 된 `DotNetReference` 개체를 통해 노출 되는 인스턴스 메서드는 개체를 범위 개체로 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-242">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="b252c-243">이는 Blazor 서버 앱에서 사용 하는 모든 DI 서비스에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-243">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="b252c-244">정적 메서드의 경우 응용 프로그램이 서버 인스턴스의 모든 사용자에 게 의도적으로 상태를 명시적으로 공유 하는 경우를 제외 하 고는 클라이언트에 대해 범위를 지정할 수 없는 상태를 설정 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-244">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="b252c-245">사용자 제공 데이터를 매개 변수에서 JavaScript 호출로 전달 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b252c-245">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="b252c-246">매개 변수에 데이터를 전달 해야 하는 경우에는 JavaScript 코드가 [XSS (사이트 간 스크립팅)](#cross-site-scripting-xss) 취약성을 일으키지 않고 데이터 전달을 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-246">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="b252c-247">예를 들어, 요소의 `innerHTML` 속성을 설정 하 여 사용자 제공 데이터를 DOM (문서 개체 모델)에 쓰지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-247">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="b252c-248">`eval` 및 기타 안전 하지 않은 JavaScript 기본 형식을 사용 하지 않으려면 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-248">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="b252c-249">프레임 워크의 디스패치 구현 위에 .NET 호출의 사용자 지정 디스패치를 구현 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b252c-249">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="b252c-250">.NET 메서드를 브라우저에 노출 하는 것이 고급 시나리오 이며 일반적인 Blazor 개발에는 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-250">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="b252c-251">이벤트</span><span class="sxs-lookup"><span data-stu-id="b252c-251">Events</span></span>

<span data-ttu-id="b252c-252">이벤트는 Blazor 서버 앱에 대 한 진입점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-252">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="b252c-253">웹 앱에서 끝점을 보호 하는 것과 동일한 규칙이 Blazor Server 앱에서 이벤트 처리에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-253">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="b252c-254">악의적인 클라이언트는 이벤트에 대 한 페이로드로 전송 하려는 모든 데이터를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-254">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="b252c-255">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="b252c-255">For example:</span></span>

* <span data-ttu-id="b252c-256">`<select>`에 대 한 변경 이벤트는 앱이 클라이언트에 제공 하는 옵션에 포함 되지 않은 값을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-256">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="b252c-257">`<input>`는 클라이언트 쪽 유효성 검사를 무시 하 고 모든 텍스트 데이터를 서버에 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-257">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="b252c-258">앱에서 처리 하는 모든 이벤트에 대 한 데이터의 유효성을 검사 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-258">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="b252c-259">Blazor framework [forms 구성 요소](xref:blazor/forms-validation) 는 기본 유효성 검사를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-259">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="b252c-260">앱에서 사용자 지정 양식 구성 요소를 사용 하는 경우 적절 한 이벤트 데이터의 유효성을 검사 하기 위해 사용자 지정 코드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-260">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="b252c-261"> 서버 이벤트는 비동기적 이므로 새 렌더링을 생성 하 여 앱이 반응할 시간이 있기 전에 여러 이벤트를 서버로 디스패치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-261"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="b252c-262">이는 고려해 야 할 몇 가지 보안 문제를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-262">This has some security implications to consider.</span></span> <span data-ttu-id="b252c-263">앱에서 클라이언트 작업을 제한 하는 작업은 이벤트 처리기 내에서 수행 해야 하며 현재 렌더링 된 뷰 상태에 종속 되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-263">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="b252c-264">사용자가 카운터를 최대 3 번 증가 시킬 수 있도록 하는 카운터 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-264">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="b252c-265">카운터를 증가 시키는 단추는 `count`값에 따라 조건부로 결정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-265">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="b252c-266">클라이언트는 프레임 워크에서이 구성 요소의 새 렌더링을 생성 하기 전에 하나 이상의 증분 이벤트를 디스패치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-266">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="b252c-267">따라서 단추가 UI에 의해 빠르게 제거 되지 않기 때문에 사용자가 `count`를 *세 번 이상* 증가 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-267">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="b252c-268">다음 예제에서는 3 개의 `count` 증분에 대 한 제한을 얻기 위한 올바른 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-268">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="b252c-269">처리기 내에 `if (count < 3) { ... }` check를 추가 하 여 `count` 증가값은 현재 앱 상태를 기준으로 결정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-269">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="b252c-270">이러한 결정은 이전 예제에서와 같이 일시적으로 오래 되었을 수 있는 UI의 상태를 기반으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-270">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="b252c-271">여러 디스패치 방지</span><span class="sxs-lookup"><span data-stu-id="b252c-271">Guard against multiple dispatches</span></span>

<span data-ttu-id="b252c-272">이벤트 콜백에서 외부 서비스 또는 데이터베이스에서 데이터를 가져오는 것과 같이 장기 실행 작업을 비동기적으로 호출 하는 경우 가드를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-272">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="b252c-273">가드는 작업이 진행 중인 동안 시각적 피드백을 사용 하 여 사용자가 여러 작업을 큐에 대기 시킬 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-273">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="b252c-274">다음 구성 요소 코드는 서버에서 데이터를 가져오는 `GetForecastAsync` 동안 `true` `isLoading` 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-274">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="b252c-275">`isLoading` `true`되는 동안에는 UI에서 단추가 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-275">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="b252c-276">이전 예제에서 보여 주는 가드 패턴은 백그라운드 작업이 `async`-`await` 패턴으로 비동기적으로 실행 되는 경우 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-276">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="b252c-277">조기에 취소 하 고 사용 방지-삭제 후</span><span class="sxs-lookup"><span data-stu-id="b252c-277">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="b252c-278">[여러 디스패치 방지](#guard-against-multiple-dispatches) 섹션에 설명 된 대로 가드를 사용 하는 것 외에도 <xref:System.Threading.CancellationToken> 사용 하 여 구성 요소가 삭제 될 때 장기 실행 작업을 취소 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-278">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="b252c-279">이 방법에는 구성 요소에서 *삭제 후 사용* 을 방지 하는 추가 혜택이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-279">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        CancellationTokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="b252c-280">많은 양의 데이터를 생성 하는 이벤트 방지</span><span class="sxs-lookup"><span data-stu-id="b252c-280">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="b252c-281">`oninput` 또는 `onscroll`와 같은 일부 DOM 이벤트는 많은 양의 데이터를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-281">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="b252c-282">Blazor server 앱에서는 이러한 이벤트를 사용 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b252c-282">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="b252c-283">추가 보안 지침</span><span class="sxs-lookup"><span data-stu-id="b252c-283">Additional security guidance</span></span>

<span data-ttu-id="b252c-284">ASP.NET Core apps를 보호 하기 위한 지침은 Blazor Server 앱에 적용 되며 다음 섹션에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-284">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="b252c-285">로깅 및 중요 한 데이터</span><span class="sxs-lookup"><span data-stu-id="b252c-285">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="b252c-286">HTTPS를 사용 하 여 전송 중인 정보 보호</span><span class="sxs-lookup"><span data-stu-id="b252c-286">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="b252c-287">[XSS (사이트 간 스크립팅](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="b252c-287">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="b252c-288">원본 간 보호</span><span class="sxs-lookup"><span data-stu-id="b252c-288">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="b252c-289">클릭-킹</span><span class="sxs-lookup"><span data-stu-id="b252c-289">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="b252c-290">열려 있는 리디렉션</span><span class="sxs-lookup"><span data-stu-id="b252c-290">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="b252c-291">로깅 및 중요 한 데이터</span><span class="sxs-lookup"><span data-stu-id="b252c-291">Logging and sensitive data</span></span>

<span data-ttu-id="b252c-292">클라이언트와 서버 간의 JS interop 상호 작용은 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스를 사용 하 여 서버 로그에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-292">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="b252c-293">는 실제 이벤트 또는 JS interop 입력 및 출력과 같은 중요 한 정보를 기록 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-293"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="b252c-294">서버에서 오류가 발생 하면 프레임 워크가 클라이언트에 알리고 세션을 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-294">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="b252c-295">기본적으로 클라이언트는 브라우저의 개발자 도구에서 볼 수 있는 일반 오류 메시지를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-295">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="b252c-296">클라이언트 쪽 오류는 호출 스택을 포함 하지 않고 오류의 원인에 대 한 세부 정보를 제공 하지 않지만 서버 로그에는 이러한 정보가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-296">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="b252c-297">개발을 위해 자세한 오류를 사용 하 여 클라이언트에서 중요 한 오류 정보를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-297">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="b252c-298">다음을 사용 하 여 자세한 오류 사용:</span><span class="sxs-lookup"><span data-stu-id="b252c-298">Enable detailed errors with:</span></span>

* <span data-ttu-id="b252c-299">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="b252c-299">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="b252c-300">구성 키를 `DetailedErrors` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-300">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="b252c-301">예를 들어 `ASPNETCORE_DETAILEDERRORS` 환경 변수를 `true`값으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-301">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="b252c-302">인터넷의 클라이언트에 오류 정보를 노출 하는 것은 항상 피해 야 하는 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-302">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="b252c-303">HTTPS를 사용 하 여 전송 중인 정보 보호</span><span class="sxs-lookup"><span data-stu-id="b252c-303">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="b252c-304"> Server는 클라이언트와 서버 간의 통신에 SignalR를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-304"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="b252c-305"> 서버는 일반적으로 SignalR 협상 하는 전송 (일반적으로 Websocket)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-305"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="b252c-306"> 서버는 서버와 클라이언트 간에 전송 되는 데이터의 무결성과 기밀성을 보장 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-306"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="b252c-307">항상 HTTPS를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-307">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="b252c-308">사이트 간 스크립팅 (XSS)</span><span class="sxs-lookup"><span data-stu-id="b252c-308">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="b252c-309">XSS (교차 사이트 스크립팅)를 사용 하면 권한이 없는 파티가 브라우저의 컨텍스트에서 임의 논리를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-309">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="b252c-310">손상 된 앱은 잠재적으로 클라이언트에서 임의 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-310">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="b252c-311">이 취약점을 사용 하 여 서버에 대해 많은 악의적인 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-311">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="b252c-312">서버에 가짜/잘못 된 이벤트를 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-312">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="b252c-313">디스패치 실패/잘못 된 렌더링 완료</span><span class="sxs-lookup"><span data-stu-id="b252c-313">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="b252c-314">렌더링 완료를 디스패치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-314">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="b252c-315">JavaScript에서 .NET으로의 interop 호출을 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-315">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="b252c-316">.NET에서 JavaScript로의 interop 호출에 대 한 응답을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-316">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="b252c-317">.NET을 JS interop 결과로 디스패치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-317">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="b252c-318">Blazor 서버 프레임 워크는 위의 몇 가지 위협 으로부터 보호 하는 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-318">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="b252c-319">클라이언트에서 렌더링 일괄 처리를 승인 하지 않으면 새 UI 업데이트 생성을 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-319">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="b252c-320">`CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-320">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="b252c-321">클라이언트에서 응답을 받지 않고 1 분 후에 .NET에서 JavaScript 호출 시간을 초과 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-321">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="b252c-322">`CircuitOptions.JSInteropDefaultCallTimeout`으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-322">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="b252c-323">JS interop 중 브라우저에서 들어오는 모든 입력에 대 한 기본 유효성 검사를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-323">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="b252c-324">.Net 참조는 유효 하며 .NET 메서드에서 예상 되는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-324">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="b252c-325">데이터 형식이 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-325">The data isn't malformed.</span></span>
  * <span data-ttu-id="b252c-326">메서드에 대 한 올바른 인수 개수가 페이로드에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-326">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="b252c-327">메서드를 호출 하기 전에 인수나 결과를 올바르게 deserialize 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-327">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="b252c-328">브라우저에서 들어오는 이벤트의 모든 입력에 기본 유효성 검사를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-328">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="b252c-329">이벤트의 형식이 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-329">The event has a valid type.</span></span>
  * <span data-ttu-id="b252c-330">이벤트에 대 한 데이터를 deserialize 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-330">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="b252c-331">이벤트와 연결 된 이벤트 처리기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-331">There's an event handler associated with the event.</span></span>

<span data-ttu-id="b252c-332">프레임 워크에서 구현 하는 보호 기능 외에도 개발자가 앱을 코딩 하 여 위협 으로부터 보호 하 고 적절 한 조치를 취해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-332">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="b252c-333">이벤트를 처리할 때 항상 데이터의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-333">Always validate data when handling events.</span></span>
* <span data-ttu-id="b252c-334">잘못 된 데이터를 받을 때 적절 한 조치를 취합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-334">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="b252c-335">데이터를 무시 하 고 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-335">Ignore the data and return.</span></span> <span data-ttu-id="b252c-336">이렇게 하면 앱에서 요청을 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-336">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="b252c-337">앱이 입력을 불법 하 고 합법적인 클라이언트에서 생성할 수 없는 것으로 확인 되 면 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-337">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="b252c-338">예외를 throw 하면 회로가 종료 되 고 세션이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-338">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="b252c-339">로그에 포함 된 렌더 일괄 처리 완료에서 제공 하는 오류 메시지를 신뢰 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-339">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="b252c-340">클라이언트는이 오류를 제공 하며 클라이언트를 손상 시킬 수 있기 때문에 일반적으로 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-340">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="b252c-341">JavaScript와 .NET 메서드 사이에서 한 방향으로 JS interop 호출의 입력을 신뢰 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-341">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="b252c-342">응용 프로그램은 인수나 결과가 올바르게 deserialize 되더라도 인수 및 결과의 내용이 유효한 지 확인 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-342">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="b252c-343">XSS 취약성이 있는 경우 앱은 렌더링 된 페이지에 사용자 입력을 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-343">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="b252c-344"> 서버 구성 요소는 *razor* 파일의 태그가 절차적 C# 논리로 변환 되는 컴파일 시간 단계를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-344"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="b252c-345">런타임에 논리는 C# 요소, 텍스트 및 자식 구성 요소를 설명 하는 *렌더링 트리* 를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-345">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="b252c-346">이는 JavaScript 명령 시퀀스를 통해 브라우저의 DOM에 적용 됩니다. 또는 렌더링 되지 않는 경우 HTML로 serialize 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-346">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="b252c-347">텍스트를 쓸 수 있는 명령을 통해 DOM에 Razor 구문를 추가 하기 때문에 일반 Razor 구문 (예: `@someStringValue`)를 통해 렌더링 된 사용자 입력은 XSS 취약성을 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-347">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="b252c-348">값에 HTML 태그가 포함 된 경우에도이 값은 정적 텍스트로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-348">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="b252c-349">렌더링을 렌더링 하면 출력이 HTML로 인코딩됩니다. 또한 콘텐츠가 정적 텍스트로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-349">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="b252c-350">스크립트 태그는 허용 되지 않으며 앱의 구성 요소 렌더링 트리에 포함 되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-350">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="b252c-351">스크립트 태그가 구성 요소의 태그에 포함 되 면 컴파일 타임 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-351">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="b252c-352">구성 요소 작성자는 Razor를 C# 사용 하지 않고에서 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-352">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="b252c-353">구성 요소 작성자는 출력을 내보낼 때 올바른 Api를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-353">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="b252c-354">예를 들어 `builder.AddContent(0, someUserSuppliedString)`를 사용 합니다. 후자의 경우에는 XSS 취약점을 만들 수 있으므로 `builder.AddMarkupContent(0, someUserSuppliedString)`*하지 않습니다* .</span><span class="sxs-lookup"><span data-stu-id="b252c-354">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="b252c-355">XSS 공격 으로부터 보호 하는 과정에서 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP)와 같은 xss 완화를 구현 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-355">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="b252c-356">자세한 내용은 <xref:security/cross-site-scripting>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-356">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="b252c-357">원본 간 보호</span><span class="sxs-lookup"><span data-stu-id="b252c-357">Cross-origin protection</span></span>

<span data-ttu-id="b252c-358">원본 간 공격은 서버에 대해 작업을 수행 하는 다른 원본의 클라이언트와 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-358">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="b252c-359">악의적인 동작은 일반적으로 GET 요청 또는 양식 게시 (사이트 간 요청 위조, CSRF) 이지만 악의적인 WebSocket을 여는 것도 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-359">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="b252c-360"> Server 앱 [은 허브 프로토콜을 사용 하는 다른 SignalR 앱과 동일한 보증](xref:signalr/security)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-360"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* <span data-ttu-id="b252c-361">이를 방지 하기 위해 추가 조치를 취해야 하는 경우를 제외 하 고는 Blazor 서버 앱에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-361">Blazor Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="b252c-362">크로스-원본 액세스를 사용 하지 않도록 설정 하려면 파이프라인에 CORS 미들웨어를 추가 하 고 Blazor 끝점 메타 데이터에 `DisableCorsAttribute`를 추가 하거나 [크로스-원본 리소스 공유에 대 한 SignalR를 구성](xref:signalr/security#cross-origin-resource-sharing)하 여 허용 되는 원본 집합을 제한 하 여 끝점에서 cors를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-362">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="b252c-363">CORS를 사용 하는 경우 CORS 구성에 따라 앱을 보호 하기 위해 추가 단계가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-363">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="b252c-364">CORS를 전역적으로 사용할 수 있는 경우 `hub.MapBlazorHub()`를 호출한 후에 `DisableCorsAttribute` 메타 데이터를 끝점 메타 데이터에 추가 하 여 Blazor 서버 허브에 대해 CORS를 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-364">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="b252c-365">자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-365">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="b252c-366">클릭-킹</span><span class="sxs-lookup"><span data-stu-id="b252c-366">Click-jacking</span></span>

<span data-ttu-id="b252c-367">킹를 클릭 하면 사용자가 공격을 받는 사이트에서 작업을 수행 하는 것을 유도할 수 있도록 사이트를 다른 원본의 사이트 내에 `<iframe>` 렌더링 하는 작업이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-367">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="b252c-368">앱이 `<iframe>`내에서 렌더링 되지 않도록 보호 하려면 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 및 `X-Frame-Options` 헤더를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-368">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="b252c-369">자세한 내용은 [MDN 웹 문서: X 프레임-옵션](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-369">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="b252c-370">열려 있는 리디렉션</span><span class="sxs-lookup"><span data-stu-id="b252c-370">Open redirects</span></span>

<span data-ttu-id="b252c-371">Blazor Server 앱 세션이 시작 되 면 서버는 세션 시작의 일부로 전송 된 Url의 기본 유효성 검사를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-371">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="b252c-372">프레임 워크는 회로를 설정 하기 전에 기준 URL이 현재 URL의 부모 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-372">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="b252c-373">프레임 워크에서 추가 검사를 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-373">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="b252c-374">사용자가 클라이언트에서 링크를 선택 하면 링크에 대 한 URL이 서버로 전송 되어 수행할 작업을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-374">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="b252c-375">예를 들어 응용 프로그램은 클라이언트 쪽 탐색을 수행 하거나 새 위치로 이동 하도록 브라우저에 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-375">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="b252c-376">구성 요소는 `NavigationManager`사용 하 여 프로그래밍 방식으로 탐색 요청을 트리거할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-376">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="b252c-377">이러한 시나리오에서 응용 프로그램은 클라이언트 쪽 탐색을 수행 하거나 브라우저에 새 위치로 이동 하는 것을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-377">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="b252c-378">구성 요소는 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-378">Components must:</span></span>

* <span data-ttu-id="b252c-379">탐색 호출 인수의 일부로 사용자 입력을 사용 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b252c-379">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="b252c-380">응용 프로그램에서 대상이 허용 되는지 확인 하기 위해 인수의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-380">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="b252c-381">그렇지 않으면 악의적인 사용자가 브라우저에서 공격자 제어 사이트로 이동 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-381">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="b252c-382">이 시나리오에서 공격자는 `NavigationManager.Navigate` 메서드 호출의 일부로 사용자 입력을 사용 하 여 앱을 트릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-382">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="b252c-383">앱의 일부로 링크를 렌더링 하는 경우에도이 조언이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-383">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="b252c-384">가능 하면 상대 링크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-384">If possible, use relative links.</span></span>
* <span data-ttu-id="b252c-385">페이지에 포함 하기 전에 절대 링크 대상이 유효한 지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-385">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="b252c-386">자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-386">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="b252c-387">인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="b252c-387">Authentication and authorization</span></span>

<span data-ttu-id="b252c-388">인증 및 권한 부여에 대 한 지침은 <xref:security/blazor/index>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b252c-388">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="b252c-389">보안 검사 목록</span><span class="sxs-lookup"><span data-stu-id="b252c-389">Security checklist</span></span>

<span data-ttu-id="b252c-390">다음 보안 고려 사항 목록은 포괄적이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-390">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="b252c-391">이벤트의 인수 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-391">Validate arguments from events.</span></span>
* <span data-ttu-id="b252c-392">JS interop 호출에서 입력 및 결과의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-392">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="b252c-393">.NET to JS interop 호출에 대 한 사용자 입력 (또는 미리 유효성 검사)을 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-393">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="b252c-394">클라이언트에서 바인딩되지 않은 메모리 양을 할당 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-394">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="b252c-395">구성 요소 내의 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-395">Data within the component.</span></span>
  * <span data-ttu-id="b252c-396">`DotNetObject` 참조를 클라이언트에 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-396">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="b252c-397">여러 디스패치를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-397">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="b252c-398">구성 요소가 삭제 될 때 장기 실행 작업을 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-398">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="b252c-399">많은 양의 데이터를 생성 하는 이벤트를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-399">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="b252c-400">`NavigationManager.Navigate`에 대 한 호출의 일부로 사용자 입력을 사용 하지 않도록 하 고, 피할 수 없는 경우 허용 되는 원본 집합에 대 한 사용자 입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-400">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="b252c-401">UI 상태를 기준으로 권한 부여를 결정 하지 않고 구성 요소 상태 에서만 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-401">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="b252c-402">XSS 공격 으로부터 보호 하기 위해 [CSP (콘텐츠 보안 정책)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-402">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="b252c-403">CSP 및 [X 프레임 옵션](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) 을 사용 하 여 킹를 방지 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-403">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="b252c-404">Cors를 사용 하도록 설정 하거나 Blazor 앱에 대해 CORS를 명시적으로 사용 하지 않도록 설정할 때 CORS 설정이 적절 한지 확인</span><span class="sxs-lookup"><span data-stu-id="b252c-404">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="b252c-405">Blazor 앱에 대 한 서버 쪽 제한이 허용 되지 않는 위험 수준 없이 허용 가능한 사용자 환경을 제공 하는지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b252c-405">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
