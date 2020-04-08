---
title: ASP.NET Core Blazor 상태 관리
author: guardrex
description: Blazor 서버 앱에서 상태를 유지하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218871"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="5c8c3-103">ASP.NET Core Blazor 상태 관리</span><span class="sxs-lookup"><span data-stu-id="5c8c3-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="5c8c3-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="5c8c3-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="5c8c3-105"> 서버는 상태 저장 앱 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="5c8c3-106">대체로 앱은 서버 연결을 지속해서 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="5c8c3-107">사용자 상태는 서버 메모리의 ‘회로’에 저장됩니다. </span><span class="sxs-lookup"><span data-stu-id="5c8c3-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="5c8c3-108">사용자 회로에 대해 저장되는 상태의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="5c8c3-109">렌더링된 UI - 구성 요소 인스턴스의 계층 구조 및 가장 최근 렌더링 출력</span><span class="sxs-lookup"><span data-stu-id="5c8c3-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="5c8c3-110">구성 요소 인스턴스의 모든 필드 및 속성 값</span><span class="sxs-lookup"><span data-stu-id="5c8c3-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="5c8c3-111">회로로 범위가 지정된 [DI](xref:fundamentals/dependency-injection)(종속성 주입) 서비스 인스턴스에 저장된 데이터</span><span class="sxs-lookup"><span data-stu-id="5c8c3-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="5c8c3-112">이 문서에서는 Blazor 서버 앱의 상태 지속성을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="5c8c3-113"> WebAssembly 앱은 [브라우저의 클라이언트 쪽 상태 지속성](#client-side-in-the-browser)을 활용할 수 있지만, 이 문서의 범위를 벗어난 사용자 지정 솔루션 또는 타사 패키지를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="5c8c3-114"> 회로</span><span class="sxs-lookup"><span data-stu-id="5c8c3-114"> circuits</span></span>

<span data-ttu-id="5c8c3-115">사용자에게 임시 네트워크 연결 손실이 발생하는 경우, Blazor는 사용자가 앱을 계속 사용할 수 있도록 사용자를 원래 회로에 다시 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="5c8c3-116">그러나 사용자를 서버 메모리의 원래 회로에 다시 연결할 수 없는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="5c8c3-117">서버는 연결이 끊어진 회로를 계속 유지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="5c8c3-118">시간 제한 이후 또는 서버에 메모리 압력이 발생할 경우 서버는 연결이 끊어진 회로를 해제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="5c8c3-119">부하가 분산된 다중 서버 배포 환경에서는 지정된 시간에 요청을 처리하는 서버를 사용할 수 없게 되는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="5c8c3-120">개별 서버에서 오류가 발생하거나, 전체 요청 볼륨을 처리하는 데 더는 필요하지 않은 경우 자동으로 제거될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="5c8c3-121">사용자가 다시 연결할 때 원본 서버를 사용하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="5c8c3-122">사용자는 브라우저를 닫았다가 다시 열거나 페이지를 다시 로드할 수 있으며, 그러면 브라우저의 메모리에 저장된 모든 상태가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="5c8c3-123">예를 들어 JavaScript interop 호출을 통해 설정된 값이 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="5c8c3-124">사용자를 원래 회로에 다시 연결할 수 없는 경우, 사용자는 빈 상태로 새 회로를 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="5c8c3-125">이 경우 데스크톱 앱을 닫았다가 다시 여는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="5c8c3-126">회로 간 상태 유지</span><span class="sxs-lookup"><span data-stu-id="5c8c3-126">Preserve state across circuits</span></span>

<span data-ttu-id="5c8c3-127">일부 시나리오에서는 회로 간에 상태를 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="5c8c3-128">앱은 다음과 같은 경우 사용자의 중요한 데이터를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="5c8c3-129">웹 서버를 사용할 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="5c8c3-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="5c8c3-130">사용자 브라우저에서 새 웹 서버를 사용하여 새 회로를 강제로 시작해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="5c8c3-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="5c8c3-131">일반적으로 회로 간 상태 유지는 사용자가 이미 존재하는 데이터를 단순히 읽는 것이 아니라 데이터를 적극적으로 만드는 시나리오에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="5c8c3-132">단일 회로 이상으로 상태를 유지하려면 ‘단순히 서버 메모리에 데이터를 저장해서는 안 됩니다’. </span><span class="sxs-lookup"><span data-stu-id="5c8c3-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="5c8c3-133">앱이 다른 스토리지 위치에 데이터를 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="5c8c3-134">상태 지속성은 자동이 아닙니다. 상태 저장 데이터 지속성을 구현하려면 앱을 개발할 때 해당 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="5c8c3-135">데이터 지속성은 일반적으로 사용자가 특별히 노력해서 만든 높은 가치의 상태에만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="5c8c3-136">다음 예제에서 상태를 유지하면 상업 활동의 시간 또는 지원을 절약할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="5c8c3-137">다단계 WebForm - 상태가 손실된 경우 사용자가 다단계 프로세스의 완료된 여러 단계에 대한 데이터를 다시 입력하려면 오랜 시간이 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="5c8c3-138">이 시나리오에서는 사용자가 다단계 양식을 벗어났다가 나중에 양식으로 돌아올 경우 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="5c8c3-139">쇼핑 카트 - 잠재적 수익을 나타내는, 상업적으로 중요한 앱 구성 요소를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="5c8c3-140">상태와 쇼핑 카트가 손실된 사용자는 나중에 사이트로 돌아올 때 제품 또는 서비스를 더 적게 구매할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="5c8c3-141">일반적으로 로그인 대화 상자에 입력되었으며 제출되지 않은 사용자 이름과 같이 다시 만들기 쉬운 상태를 유지할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5c8c3-142">앱은 ‘앱 상태’만 유지할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="5c8c3-142">An app can only persist *app state*.</span></span> <span data-ttu-id="5c8c3-143">구성 요소 인스턴스 및 렌더링 트리와 같은 UI는 유지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="5c8c3-144">구성 요소 및 렌더링 트리는 일반적으로 직렬화할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="5c8c3-145">트리 뷰의 펼친 노드와 같은, UI 상태와 유사한 내용을 유지하려면 동작을 직렬화 가능한 앱 상태로 모델링하는 사용자 지정 코드가 앱에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="5c8c3-146">상태를 유지할 위치</span><span class="sxs-lookup"><span data-stu-id="5c8c3-146">Where to persist state</span></span>

<span data-ttu-id="5c8c3-147">Blazor 서버 앱에는 상태를 유지하기 위한 세 가지 공통 위치가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="5c8c3-148">각 방법은 서로 다른 시나리오에 가장 적합하며, 고유한 주의 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="5c8c3-149">서버 쪽 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="5c8c3-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="5c8c3-150">URL</span><span class="sxs-lookup"><span data-stu-id="5c8c3-150">URL</span></span>](#url)
* [<span data-ttu-id="5c8c3-151">클라이언트 쪽 브라우저</span><span class="sxs-lookup"><span data-stu-id="5c8c3-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="5c8c3-152">서버 쪽 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="5c8c3-152">Server-side in a database</span></span>

<span data-ttu-id="5c8c3-153">영구적인 데이터 지속성이 필요하거나 데이터가 여러 사용자 또는 디바이스에 걸쳐 있어야 하는 경우 대체로 독립적인 서버 쪽 데이터베이스를 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="5c8c3-154">다음 옵션을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-154">Options include:</span></span>

* <span data-ttu-id="5c8c3-155">관계형 SQL 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="5c8c3-155">Relational SQL database</span></span>
* <span data-ttu-id="5c8c3-156">키-값 저장소</span><span class="sxs-lookup"><span data-stu-id="5c8c3-156">Key-value store</span></span>
* <span data-ttu-id="5c8c3-157">Blob 저장소</span><span class="sxs-lookup"><span data-stu-id="5c8c3-157">Blob store</span></span>
* <span data-ttu-id="5c8c3-158">테이블 저장소</span><span class="sxs-lookup"><span data-stu-id="5c8c3-158">Table store</span></span>

<span data-ttu-id="5c8c3-159">데이터베이스에 데이터를 저장한 후에는 사용자가 언제든지 새 회로를 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="5c8c3-160">사용자 데이터가 보존되고 모든 새 회로에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="5c8c3-161">Azure 데이터 스토리지 옵션에 대한 자세한 내용은 [Azure Storage 설명서](/azure/storage/) 및 [Azure 데이터베이스](https://azure.microsoft.com/product-categories/databases/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="5c8c3-162">URL</span><span class="sxs-lookup"><span data-stu-id="5c8c3-162">URL</span></span>

<span data-ttu-id="5c8c3-163">탐색 상태를 나타내는 임시 데이터의 경우 데이터를 URL 일부로 모델링합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="5c8c3-164">URL에 모델링된 상태의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="5c8c3-165">표시된 엔터티의 ID</span><span class="sxs-lookup"><span data-stu-id="5c8c3-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="5c8c3-166">페이징 그리드의 현재 페이지 번호</span><span class="sxs-lookup"><span data-stu-id="5c8c3-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="5c8c3-167">브라우저의 주소 표시줄 내용은 다음과 같은 경우에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="5c8c3-168">사용자가 페이지를 수동으로 다시 로드하는 경우</span><span class="sxs-lookup"><span data-stu-id="5c8c3-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="5c8c3-169">웹 서버를 사용할 수 없는 경우 - 사용자는 다른 서버에 연결하기 위해 페이지를 강제로 다시 로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="5c8c3-170">`@page` 지시문을 사용하여 URL 패턴을 정의하는 방법에 대한 자세한 내용은 <xref:blazor/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="5c8c3-171">클라이언트 쪽 브라우저</span><span class="sxs-lookup"><span data-stu-id="5c8c3-171">Client-side in the browser</span></span>

<span data-ttu-id="5c8c3-172">사용자가 적극적으로 만드는 임시 데이터의 경우, 일반적인 백업 저장소는 브라우저의 `localStorage` 및 `sessionStorage` 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="5c8c3-173">회로가 중단된 경우 앱에서 저장된 상태를 관리하거나 지울 필요가 없으므로 서버 쪽 스토리지보다 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="5c8c3-174">이 섹션에서 “클라이언트 쪽”은 [Blazor WebAssembly 호스팅 모델](xref:blazor/hosting-models#blazor-webassembly)이 아니라 브라우저의 클라이언트 쪽 시나리오를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="5c8c3-175">Blazor WebAssembly 앱에서는 `localStorage` 및 `sessionStorage`를 사용할 수 있지만, 사용자 지정 코드를 작성하거나 타사 패키지를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="5c8c3-176">`localStorage` 및 `sessionStorage`는 다음과 같은 차이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="5c8c3-177">`localStorage`는 사용자 브라우저로 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="5c8c3-178">사용자가 페이지를 다시 로드하거나 브라우저를 닫았다가 다시 열면 상태가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="5c8c3-179">사용자가 여러 개의 브라우저 탭을 여는 경우 탭 간에 상태가 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="5c8c3-180">명시적으로 지울 때까지 데이터가 `localStorage`에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="5c8c3-181">`sessionStorage`는 사용자의 브라우저 탭으로 범위가 지정됩니다. 사용자가 탭을 다시 로드하면 상태가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="5c8c3-182">사용자가 탭 또는 브라우저를 닫으면 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="5c8c3-183">사용자가 여러 개의 브라우저 탭을 여는 경우 각 탭에 독립적인 고유한 버전의 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="5c8c3-184">일반적으로 `sessionStorage`를 사용하는 것이 더 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="5c8c3-185">`sessionStorage`를 사용하면 사용자가 여러 탭을 열 때 다음과 같은 경우가 발생하는 위험을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="5c8c3-186">탭 간에 상태 스토리지의 버그</span><span class="sxs-lookup"><span data-stu-id="5c8c3-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="5c8c3-187">한 탭에서 다른 탭의 상태를 덮어쓸 때 혼동되는 동작</span><span class="sxs-lookup"><span data-stu-id="5c8c3-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="5c8c3-188">앱이 브라우저를 닫았다가 다시 열 때 상태를 유지해야 하는 경우 `localStorage`를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="5c8c3-189">브라우저 스토리지를 사용하는 경우의 주의 사항:</span><span class="sxs-lookup"><span data-stu-id="5c8c3-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="5c8c3-190">서버 쪽 데이터베이스를 사용하는 경우와 유사하게, 데이터 로드 및 저장은 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="5c8c3-191">서버 쪽 데이터베이스와 달리, 미리 렌더링 단계에서는 요청한 페이지가 브라우저에 없기 때문에 미리 렌더링하는 동안 스토리지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="5c8c3-192">몇 킬로바이트의 데이터 스토리지는 Blazor 서버 앱에 대해 유지해도 타당합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="5c8c3-193">몇 킬로바이트를 넘을 경우, 네트워크를 통해 데이터를 로드하고 저장하기 때문에 성능에 미치는 영향을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="5c8c3-194">사용자가 데이터를 보거나 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="5c8c3-195">ASP.NET Core [데이터 보호](xref:security/data-protection/introduction)는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="5c8c3-196">타사 브라우저 스토리지 솔루션</span><span class="sxs-lookup"><span data-stu-id="5c8c3-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="5c8c3-197">타사 NuGet 패키지는 `localStorage` 및 `sessionStorage` 작업을 위한 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="5c8c3-198">ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)를 투명하게 사용하는 패키지를 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="5c8c3-199">ASP.NET Core 데이터 보호는 저장된 데이터를 암호화하고, 저장된 데이터의 잠재적 변조 위험을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="5c8c3-200">JSON 직렬화된 데이터를 일반 텍스트로 저장한 경우, 사용자는 브라우저 개발자 도구를 사용하여 데이터를 확인할 수 있으며 저장된 데이터를 수정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="5c8c3-201">본질적으로 데이터가 중요하지 않을 수도 있으므로 데이터 보호가 항상 문제가 되는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="5c8c3-202">예를 들어 저장된 UI 요소 색을 읽거나 수정하는 경우 사용자 또는 조직에 중요한 보안 위험이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="5c8c3-203">사용자가 ‘중요한 데이터’를 검사하거나 변조할 수 있도록 허용하면 안 됩니다. </span><span class="sxs-lookup"><span data-stu-id="5c8c3-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="5c8c3-204">보호된 브라우저 스토리지 실험적 패키지</span><span class="sxs-lookup"><span data-stu-id="5c8c3-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="5c8c3-205">`localStorage` 및 `sessionStorage`에 대한 [데이터 보호](xref:security/data-protection/introduction) 기능을 제공하는 NuGet 패키지의 예로 [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="5c8c3-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`는 지원되지 않는 실험적 패키지로, 현재 프로덕션 사용에는 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="5c8c3-207">설치</span><span class="sxs-lookup"><span data-stu-id="5c8c3-207">Installation</span></span>

<span data-ttu-id="5c8c3-208">`Microsoft.AspNetCore.ProtectedBrowserStorage` 패키지를 설치하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="5c8c3-209">Blazor 서버 앱 프로젝트에서 [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="5c8c3-210">최상위 HTML(예: 기본 프로젝트 템플릿의 *Pages/_Host.cshtml* 파일)에서 다음 `<script>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="5c8c3-211">`Startup.ConfigureServices` 메서드에서 `AddProtectedBrowserStorage`를 호출하여 서비스 컬렉션에 `localStorage` 및 `sessionStorage` 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="5c8c3-212">구성 요소 내에서 데이터 저장 및 로드</span><span class="sxs-lookup"><span data-stu-id="5c8c3-212">Save and load data within a component</span></span>

<span data-ttu-id="5c8c3-213">브라우저 스토리지에 데이터를 로드하거나 저장해야 하는 구성 요소에서 [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component)를 사용하여 다음 중 하나의 인스턴스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="5c8c3-214">사용하려는 백업 저장소에 따라 선택이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="5c8c3-215">다음 예제에서는 `sessionStorage`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="5c8c3-216">구성 요소 대신 *_Imports.razor* 파일에 `@using` 문을 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="5c8c3-217">*_Imports.razor* 파일을 사용하면 더 큰 앱 세그먼트나 전체 앱에서 네임스페이스를 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="5c8c3-218">프로젝트 템플릿의 `Counter` 구성 요소에 `_currentCount` 값을 유지하려면 `ProtectedSessionStore.SetAsync`를 사용하도록 `IncrementCount` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="5c8c3-219">더 크고 현실적인 앱에서 개별 필드 스토리지는 가능성이 거의 없는 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="5c8c3-220">앱에서 복합 상태를 포함하는 전체 모델 개체를 저장할 가능성이 더 큽니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="5c8c3-221">`ProtectedSessionStore`는 JSON 데이터를 자동으로 직렬화 및 역직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="5c8c3-222">위의 코드 예제에서 `_currentCount` 데이터는 사용자 브라우저에 `sessionStorage['count']`로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="5c8c3-223">데이터는 일반 텍스트로 저장되지 않고 ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)로 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="5c8c3-224">브라우저의 개발자 콘솔에서 `sessionStorage['count']`를 평가하는 경우 암호화된 데이터를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="5c8c3-225">사용자가 나중에 `Counter` 구성 요소로 돌아오는 경우(완전히 새로운 회로를 사용하는 경우 포함) `_currentCount` 데이터를 복구하려면 `ProtectedSessionStore.GetAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="5c8c3-226">구성 요소의 매개 변수에 탐색 상태가 포함된 경우, `ProtectedSessionStore.GetAsync`를 호출하고 결과를 `OnInitializedAsync`가 아닌 `OnParametersSetAsync`에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="5c8c3-227">`OnInitializedAsync`는 구성 요소를 처음 인스턴스화할 때 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="5c8c3-228">나중에 사용자가 동일한 페이지를 유지하면서 다른 URL로 이동하는 경우에는 `OnInitializedAsync`가 다시 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="5c8c3-229">자세한 내용은 <xref:blazor/lifecycle>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="5c8c3-230">이 섹션의 예제는 서버에서 미리 렌더링을 사용하지 않는 경우에만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="5c8c3-231">미리 렌더링을 사용하는 경우 다음과 같은 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="5c8c3-232">지금은 JavaScript interop 호출을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="5c8c3-233">구성 요소를 미리 렌더링하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="5c8c3-234">미리 렌더링을 사용하지 않도록 설정하거나, 미리 렌더링 작업을 위한 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="5c8c3-235">미리 렌더링 작업을 위한 코드 작성 방법에 대한 자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="5c8c3-236">로드 상태 처리</span><span class="sxs-lookup"><span data-stu-id="5c8c3-236">Handle the loading state</span></span>

<span data-ttu-id="5c8c3-237">브라우저 스토리지는 비동기이므로(네트워크 연결을 통해 액세스), 데이터가 로드되고 구성 요소에서 사용할 수 있을 때까지 항상 일정 기간이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="5c8c3-238">최상의 결과를 얻으려면 빈 데이터나 기본 데이터를 표시하는 대신, 로드가 진행되는 동안 로드 상태 메시지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="5c8c3-239">한 가지 방법은 데이터가 `null`(로드 중)인지 여부를 추적하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="5c8c3-240">기본 `Counter` 구성 요소에서 개수는 `int`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="5c8c3-241">형식(`int`)에 물음표(`?`)를 추가하여 `_currentCount`를 null 허용으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="5c8c3-242">개수 및 **Increment** 단추를 무조건 표시하는 대신, 데이터가 로드된 경우에만 해당 요소를 표시하도록 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="5c8c3-243">미리 렌더링 처리</span><span class="sxs-lookup"><span data-stu-id="5c8c3-243">Handle prerendering</span></span>

<span data-ttu-id="5c8c3-244">미리 렌더링 과정과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-244">During prerendering:</span></span>

* <span data-ttu-id="5c8c3-245">사용자 브라우저에 대한 대화형 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="5c8c3-246">브라우저에 JavaScript 코드를 실행할 수 있는 페이지가 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="5c8c3-247">미리 렌더링하는 동안 `localStorage` 또는 `sessionStorage`를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="5c8c3-248">구성 요소가 스토리지와 상호 작용하려고 하면 다음과 같은 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="5c8c3-249">지금은 JavaScript interop 호출을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="5c8c3-250">구성 요소를 미리 렌더링하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="5c8c3-251">오류를 해결하는 한 가지 방법은 미리 렌더링을 사용하지 않도록 설정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="5c8c3-252">앱에서 브라우저 기반 스토리지를 많이 사용하는 경우, 일반적으로 이 옵션을 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="5c8c3-253">`localStorage` 또는 `sessionStorage`를 사용할 수 있어야 앱에서 유용한 콘텐츠를 미리 렌더링할 수 있기 때문에 미리 렌더링은 복잡성만 추가하고 앱에 도움이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="5c8c3-254">미리 렌더링을 사용하지 않도록 설정하려면 *Pages/_Host.cshtml* 파일을 열고 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)의 `render-mode`를 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="5c8c3-255">`localStorage` 또는 `sessionStorage`를 사용하지 않는 다른 페이지에는 미리 렌더링이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="5c8c3-256">미리 렌더링을 사용 상태로 유지하려면 브라우저가 회로에 연결될 때까지 로드 작업을 연기합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="5c8c3-257">다음은 카운터 값을 저장하기 위한 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="5c8c3-258">상태 유지를 공통 위치로 추출</span><span class="sxs-lookup"><span data-stu-id="5c8c3-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="5c8c3-259">많은 구성 요소가 브라우저 기반 스토리지를 사용하는 경우 상태 제공자 코드를 여러 번 다시 구현하면 코드 중복이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="5c8c3-260">코드 중복을 방지하는 한 가지 옵션은 상태 제공자 논리를 캡슐화하는 ‘상태 제공자 부모 구성 요소’를 만드는 것입니다. </span><span class="sxs-lookup"><span data-stu-id="5c8c3-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="5c8c3-261">자식 구성 요소는 상태 지속성 메커니즘과 관계없이 영구 데이터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="5c8c3-262">다음 `CounterStateProvider` 구성 요소 예제에서는 카운터 데이터가 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="5c8c3-263">`CounterStateProvider` 구성 요소는 로드가 완료될 때까지 자식 콘텐츠를 렌더링하지 않고 로드 단계를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="5c8c3-264">`CounterStateProvider` 구성 요소를 사용하려면 카운터 상태에 액세스해야 하는 다른 모든 구성 요소를 이 구성 요소 인스턴스로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="5c8c3-265">앱의 모든 구성 요소가 상태에 액세스할 수 있게 하려면 `App` 구성 요소(*App.razor*)의 `Router`를 `CounterStateProvider` 구성 요소로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="5c8c3-266">래핑된 구성 요소는 영구 카운터 상태를 받고 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="5c8c3-267">다음 `Counter` 구성 요소는 패턴을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-267">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="5c8c3-268">위의 구성 요소는 `ProtectedBrowserStorage`와 상호 작용하는 데 필요하지 않으며, “로드” 단계를 처리하지도 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="5c8c3-269">앞에서 설명한 대로 미리 렌더링을 처리하기 위해, 카운터 데이터를 사용하는 모든 구성 요소가 자동으로 미리 렌더링을 사용하도록 `CounterStateProvider`를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="5c8c3-270">자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="5c8c3-271">일반적으로 다음과 같은 경우 ‘상태 제공자 부모 구성 요소’ 패턴을 사용하는 것이 좋습니다. </span><span class="sxs-lookup"><span data-stu-id="5c8c3-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="5c8c3-272">다른 많은 구성 요소에서 상태를 사용하려는 경우</span><span class="sxs-lookup"><span data-stu-id="5c8c3-272">To consume state in many other components.</span></span>
* <span data-ttu-id="5c8c3-273">유지할 최상위 상태 개체가 하나뿐인 경우</span><span class="sxs-lookup"><span data-stu-id="5c8c3-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="5c8c3-274">여러 다른 상태 개체를 유지하고 서로 다른 위치에 있는 여러 개체 하위 집합을 사용하려면 전역적으로 상태 로드 및 저장을 처리하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5c8c3-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
