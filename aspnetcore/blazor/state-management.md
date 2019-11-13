---
title: ASP.NET Core Blazor 상태 관리
author: guardrex
description: Blazor Server 앱에서 상태를 유지 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/state-management
ms.openlocfilehash: 408d44a3f2e81a165e8b786c6d2efc9329082e30
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962820"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="42051-103">ASP.NET Core Blazor 상태 관리</span><span class="sxs-lookup"><span data-stu-id="42051-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="42051-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="42051-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="42051-105"> Server는 상태 저장 앱 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="42051-106">대부분의 경우 앱은 서버에 지속적으로 연결을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="42051-107">사용자의 상태는 *회로*의 서버 메모리에 보관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="42051-108">사용자 회로에 대해 유지 되는 상태 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="42051-109">렌더링 된 UI는 구성 요소 인스턴스의 계층 구조와 가장 최근 렌더링 출력&mdash;합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="42051-110">구성 요소 인스턴스의 모든 필드와 속성 값입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="42051-111">회로로 범위가 지정 된 [DI (종속성 주입)](xref:fundamentals/dependency-injection) 서비스 인스턴스에 저장 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="42051-112">이 문서에서는 Blazor Server 앱의 상태 지 속성에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="42051-113"> Weasembomapps 앱은 [브라우저에서 클라이언트 쪽 상태 지 속성](#client-side-in-the-browser) 을 활용할 수 있지만이 문서의 범위를 벗어나는 사용자 지정 솔루션 또는 타사 패키지를 요구 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="42051-114"> 회로</span><span class="sxs-lookup"><span data-stu-id="42051-114"> circuits</span></span>

<span data-ttu-id="42051-115">사용자가 임시 네트워크 연결 Blazor 손실을 경험 하는 경우 사용자가 앱을 계속 사용할 수 있도록 원래 회로에 사용자를 다시 연결 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="42051-116">그러나 서버 메모리의 원래 회로에 사용자를 다시 연결 하는 것은 항상 가능 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="42051-117">서버는 연결 되지 않은 회로를 계속 유지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="42051-118">서버는 시간이 초과 되거나 서버의 메모리가 부족 한 경우 연결이 끊어진 회로를 해제 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="42051-119">다중 서버, 부하 분산 된 배포 환경에서는 지정 된 시간에 서버 처리 요청을 사용할 수 없게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="42051-120">개별 서버는 전체 요청 볼륨을 더 이상 처리할 필요가 없을 때 실패할 수도 있고 자동으로 제거 될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="42051-121">사용자가 다시 연결 하려고 할 때 원본 서버를 사용 하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="42051-122">사용자가 브라우저를 닫았다가 다시 열거나 페이지를 다시 로드할 수 있습니다. 그러면 브라우저의 메모리에 저장 된 모든 상태가 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="42051-123">예를 들어 JavaScript interop 호출을 통해 설정 된 값은 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="42051-124">사용자를 원래 회로에 다시 연결할 수 없는 경우 사용자는 빈 상태로 새 회로를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="42051-125">이는 데스크톱 앱을 닫았다가 다시 여는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="42051-126">회로 간 상태 유지</span><span class="sxs-lookup"><span data-stu-id="42051-126">Preserve state across circuits</span></span>

<span data-ttu-id="42051-127">일부 시나리오에서는 회로 간에 상태를 유지 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="42051-128">앱은 다음과 같은 경우 사용자에 대 한 중요 한 데이터를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="42051-129">웹 서버를 사용할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="42051-130">사용자의 브라우저는 새 웹 서버를 사용 하 여 새 회로를 강제로 시작 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="42051-131">일반적으로 회로 간에 상태를 유지 관리 하는 것은 사용자가 이미 존재 하는 데이터를 읽는 것이 아니라 데이터를 적극적으로 만드는 시나리오에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="42051-132">단일 회로 이상의 상태를 유지 하려면 *데이터를 서버 메모리에 저장 하지 마세요*.</span><span class="sxs-lookup"><span data-stu-id="42051-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="42051-133">앱은 데이터를 다른 저장소 위치에 보관 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="42051-134">상태 지 속성은 자동&mdash;되지 않습니다. 상태 저장 데이터 지 속성을 구현 하기 위해 앱을 개발할 때 단계를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="42051-135">데이터 지 속성은 일반적으로 사용자가 소요 된 작업을 수행 하는 높은 가치의 상태에만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="42051-136">다음 예에서는 상태를 유지 하 여 상업적 활동에 시간 또는 지원을 저장할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="42051-137">다단계 webform &ndash;은 해당 상태가 손실 된 경우 사용자가 다단계 프로세스의 여러 완료 된 단계에 대 한 데이터를 다시 입력 하는 데 시간이 많이 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="42051-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="42051-138">이 시나리오에서는 사용자가 다단계 폼에서 다른 곳으로 이동 하 여 나중에 폼으로 돌아갈 경우 상태가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="42051-139">쇼핑 카트 &ndash; 잠재적인 수익을 나타내는 앱의 상업적으로 중요 한 구성 요소를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="42051-140">사용자가 자신의 상태를 상실 하 고 장바구니는 나중에 사이트로 돌아올 때 더 많은 제품이 나 서비스를 구매할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="42051-141">일반적으로 전송 되지 않은 로그인 대화 상자에 입력 한 사용자 이름과 같이 쉽게 다시 만들어진 상태를 유지할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="42051-142">앱은 *앱 상태만*유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-142">An app can only persist *app state*.</span></span> <span data-ttu-id="42051-143">구성 요소 인스턴스 및 해당 렌더링 트리와 같은 Ui는 유지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="42051-144">구성 요소 및 렌더링 트리는 일반적으로 serialize 할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="42051-145">트리 뷰에서 확장 된 노드와 같은 UI 상태와 유사한 내용을 유지 하려면 앱에 사용자 지정 코드가 있어야 동작을 serializable 앱 상태로 모델링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="42051-146">상태를 유지 하는 위치</span><span class="sxs-lookup"><span data-stu-id="42051-146">Where to persist state</span></span>

<span data-ttu-id="42051-147">Blazor Server 앱에서 상태를 유지 하기 위한 세 가지 공통 위치가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="42051-148">각 방법은 서로 다른 시나리오에 가장 적합 하며, 다음과 같은 다양 한 주의 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="42051-149">데이터베이스의 서버 쪽</span><span class="sxs-lookup"><span data-stu-id="42051-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="42051-150">URL</span><span class="sxs-lookup"><span data-stu-id="42051-150">URL</span></span>](#url)
* [<span data-ttu-id="42051-151">브라우저의 클라이언트 쪽</span><span class="sxs-lookup"><span data-stu-id="42051-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="42051-152">데이터베이스의 서버 쪽</span><span class="sxs-lookup"><span data-stu-id="42051-152">Server-side in a database</span></span>

<span data-ttu-id="42051-153">영구 데이터 지 속성 또는 여러 사용자 또는 장치에 걸쳐 있어야 하는 모든 데이터의 경우에는 독립 된 서버 쪽 데이터베이스를 선택 하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="42051-154">다음 옵션을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-154">Options include:</span></span>

* <span data-ttu-id="42051-155">관계형 SQL 데이터베이스</span><span class="sxs-lookup"><span data-stu-id="42051-155">Relational SQL database</span></span>
* <span data-ttu-id="42051-156">키-값 저장소</span><span class="sxs-lookup"><span data-stu-id="42051-156">Key-value store</span></span>
* <span data-ttu-id="42051-157">Blob 저장소</span><span class="sxs-lookup"><span data-stu-id="42051-157">Blob store</span></span>
* <span data-ttu-id="42051-158">테이블 저장소</span><span class="sxs-lookup"><span data-stu-id="42051-158">Table store</span></span>

<span data-ttu-id="42051-159">데이터를 데이터베이스에 저장 한 후에는 사용자가 언제 든 지 새 회로를 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="42051-160">사용자의 데이터는 유지 되 고 모든 새 회로에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="42051-161">Azure 데이터 저장소 옵션에 대 한 자세한 내용은 [Azure Storage 설명서](/azure/storage/) 및 [azure 데이터베이스](https://azure.microsoft.com/product-categories/databases/)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="42051-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="42051-162">URL</span><span class="sxs-lookup"><span data-stu-id="42051-162">URL</span></span>

<span data-ttu-id="42051-163">탐색 상태를 나타내는 임시 데이터의 경우 데이터를 URL의 일부로 모델링 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="42051-164">URL에서 모델링 된 상태의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="42051-165">표시 된 엔터티의 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="42051-166">페이지 눈금의 현재 페이지 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="42051-167">브라우저의 주소 표시줄의 내용은 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="42051-168">사용자가 페이지를 수동으로 다시 로드 하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="42051-169">웹 서버를 사용할 수 없게 되 면 사용자는 다른 서버에 연결 하기 위해 페이지를 강제로 다시 로드 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="42051-170">`@page` 지시어를 사용 하 여 URL 패턴을 정의 하는 방법에 대 한 자세한 내용은 <xref:blazor/routing>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="42051-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="42051-171">브라우저의 클라이언트 쪽</span><span class="sxs-lookup"><span data-stu-id="42051-171">Client-side in the browser</span></span>

<span data-ttu-id="42051-172">사용자가 적극적으로 만드는 임시 데이터의 경우 일반적인 백업 저장소는 브라우저의 `localStorage` 및 `sessionStorage` 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="42051-173">회로를 중단 한 경우에는 저장 된 상태를 관리 하거나 지울 필요가 없습니다 .이는 서버 쪽 저장소의 장점입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="42051-174">이 섹션의 "클라이언트 쪽"은 [Blazor Weasembom호스팅 모델이](xref:blazor/hosting-models#blazor-webassembly)아닌 브라우저의 클라이언트 쪽 시나리오를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="42051-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="42051-175">`localStorage` 및 `sessionStorage`는 Blazor Weasembomapps에서 사용할 수 있지만 사용자 지정 코드를 작성 하거나 타사 패키지를 사용 하는 경우에만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="42051-176">`localStorage` 및 `sessionStorage`은 다음과 같이 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="42051-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="42051-177">`localStorage`은 사용자의 브라우저로 범위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="42051-178">사용자가 페이지를 다시 로드 하거나 브라우저를 닫고 다시 열면 상태가 지속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="42051-179">사용자가 여러 브라우저 탭을 여는 경우 상태는 탭에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="42051-180">데이터는 명시적으로 지울 때까지 `localStorage`에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="42051-181">`sessionStorage`은 사용자의 브라우저 탭으로 범위가 지정 됩니다. 사용자가 탭을 다시 로드 하면 상태가 지속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="42051-182">사용자가 탭 또는 브라우저를 닫으면 상태가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="42051-183">사용자가 여러 브라우저 탭을 여는 경우 각 탭에는 고유한 독립 버전의 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="42051-184">일반적으로 `sessionStorage`은를 사용 하는 것이 더 안전 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="42051-185">`sessionStorage`은 사용자가 여러 탭을 열고 다음을 발견할 위험을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="42051-186">상태 저장소의 여러 탭에 있는 버그</span><span class="sxs-lookup"><span data-stu-id="42051-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="42051-187">탭이 다른 탭의 상태를 덮어쓸 때 혼동 되는 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="42051-188">앱이 브라우저를 닫고 다시 여는 동안 상태를 유지 해야 하는 경우 `localStorage`을 선택 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="42051-189">브라우저 저장소 사용에 대 한 주의 사항:</span><span class="sxs-lookup"><span data-stu-id="42051-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="42051-190">서버측 데이터베이스를 사용 하는 것과 유사 하 게 데이터 로드 및 저장은 비동기식입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="42051-191">서버 쪽 데이터베이스와 달리, 요청 된 페이지는 렌더링 전 단계에서 브라우저에 존재 하지 않기 때문에 렌더링 중에는 저장소를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="42051-192">몇 킬로바이트의 데이터 저장소는 Blazor 서버 앱에 대해 유지 하는 것이 합리적입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="42051-193">네트워크를 통해 데이터를 로드 하 고 저장 하기 때문에 몇 킬로바이트 이상에서 성능 영향을 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="42051-194">사용자가 데이터를 보거나 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="42051-195">[데이터 보호가](xref:security/data-protection/introduction) ASP.NET Core 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="42051-196">타사 브라우저 저장소 솔루션</span><span class="sxs-lookup"><span data-stu-id="42051-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="42051-197">타사 NuGet 패키지는 `localStorage` 및 `sessionStorage`을 사용 하기 위한 Api를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="42051-198">ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)를 투명 하 게 사용 하는 패키지를 선택 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="42051-199">ASP.NET Core 데이터 보호는 저장 된 데이터를 암호화 하 고 저장 된 데이터로 변조 될 수 있는 잠재적 위험을 줄여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="42051-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="42051-200">JSON 직렬화 된 데이터를 일반 텍스트로 저장 하는 경우 사용자는 브라우저 개발자 도구를 사용 하 여 데이터를 확인 하 고 저장 된 데이터를 수정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="42051-201">데이터를 보호 하는 것은 본질적으로 중요 하지 않을 수 있기 때문에 항상 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="42051-202">예를 들어 UI 요소의 저장 된 색을 읽거나 수정 하는 경우 사용자 또는 조직에 중요 한 보안 위험이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="42051-203">사용자가 *중요 한 데이터*를 검사 하거나 조작할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="42051-204">보호 된 브라우저 저장소 실험적 패키지</span><span class="sxs-lookup"><span data-stu-id="42051-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="42051-205">`localStorage` 및 `sessionStorage`에 대 한 [데이터 보호](xref:security/data-protection/introduction) 를 제공 하는 NuGet 패키지의 예는 [ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="42051-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`은 현재 프로덕션 사용에 적합 하지 않은 지원 되지 않는 실험적 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="42051-207">설치</span><span class="sxs-lookup"><span data-stu-id="42051-207">Installation</span></span>

<span data-ttu-id="42051-208">`Microsoft.AspNetCore.ProtectedBrowserStorage` 패키지를 설치 하려면:</span><span class="sxs-lookup"><span data-stu-id="42051-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="42051-209">Blazor Server 앱 프로젝트에서 [AspNetCore에 ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="42051-210">최상위 HTML (예: 기본 프로젝트 템플릿의 *Pages/_Host* 파일)에서 다음 `<script>` 태그를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="42051-211">`Startup.ConfigureServices` 메서드에서 `AddProtectedBrowserStorage`를 호출 하 여 서비스 컬렉션에 `localStorage` 및 `sessionStorage` 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="42051-212">구성 요소 내에서 데이터 저장 및 로드</span><span class="sxs-lookup"><span data-stu-id="42051-212">Save and load data within a component</span></span>

<span data-ttu-id="42051-213">브라우저 저장소에 데이터를 로드 하거나 저장 해야 하는 모든 구성 요소에서 [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) 을 사용 하 여 다음 중 하나의 인스턴스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-213">In any component that requires loading or saving data to browser storage, use [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="42051-214">사용 하려는 백업 저장소에 따라 선택이 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="42051-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="42051-215">다음 예제에서는 `sessionStorage`이 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-215">In the following example, `sessionStorage` is used:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="42051-216">`@using` 문은 구성 요소가 아닌 *_Imports razor* 파일에 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="42051-217">*_Imports razor* 파일을 사용 하면 앱의 더 큰 세그먼트 또는 전체 앱에서 네임 스페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="42051-218">프로젝트 템플릿의 `Counter` 구성 요소에서 `currentCount` 값을 유지 하려면 `ProtectedSessionStore.SetAsync`을 사용 하도록 `IncrementCount` 메서드를 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-218">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="42051-219">더 크고 현실적인 앱에서 개별 필드의 저장은 거의 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="42051-220">앱은 복합 상태를 포함 하는 전체 모델 개체를 저장할 가능성이 높습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="42051-221">`ProtectedSessionStore`은 JSON 데이터를 자동으로 직렬화 및 deserialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="42051-222">위의 코드 예제에서 `currentCount` 데이터는 사용자의 브라우저에 `sessionStorage['count']` 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-222">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="42051-223">데이터는 일반 텍스트로 저장 되지 않고 ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)를 사용 하 여 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="42051-224">브라우저의 개발자 콘솔에서 `sessionStorage['count']`를 평가 하는 경우 암호화 된 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="42051-225">사용자가 나중에 `Counter` 구성 요소로 반환 하는 경우 `currentCount` 데이터를 복구 하려면 (완전히 새로운 회로를 사용 하는 경우 포함) `ProtectedSessionStore.GetAsync`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-225">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="42051-226">구성 요소의 매개 변수가 탐색 상태를 포함 하는 경우 `ProtectedSessionStore.GetAsync`을 호출 하 고 결과를 `OnInitializedAsync`가 아닌 `OnParametersSetAsync`로 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="42051-227">`OnInitializedAsync`은 구성 요소가 처음 인스턴스화될 때 한 번만 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="42051-228">`OnInitializedAsync`은 나중에 사용자가 다른 URL로 이동 하는 경우 나중에 다시 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span>

> [!WARNING]
> <span data-ttu-id="42051-229">이 섹션의 예제는 서버에서 렌더링을 사용 하도록 설정 하지 않은 경우에만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-229">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="42051-230">렌더링을 사용 하는 경우 다음과 유사한 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-230">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="42051-231">지금은 JavaScript interop 호출을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-231">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="42051-232">구성 요소가 미리 렌더링 된 되 고 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-232">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="42051-233">렌더링 기능을 사용 하지 않도록 설정 하거나 다른 코드를 추가 하 여 렌더링을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-233">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="42051-234">렌더링을 사용 하는 코드 작성에 대해 자세히 알아보려면 [핸들 렌더링](#handle-prerendering) 안 함 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="42051-234">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="42051-235">로드 상태를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-235">Handle the loading state</span></span>

<span data-ttu-id="42051-236">브라우저 저장소는 비동기 (네트워크 연결을 통해 액세스 됨) 이기 때문에 데이터를 로드 하 고 구성 요소에서 사용할 수 있을 때까지 항상 기간이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-236">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="42051-237">최상의 결과를 위해 빈 데이터 나 기본 데이터를 표시 하는 대신 로드가 진행 되는 동안 로드 상태 메시지를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-237">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="42051-238">한 가지 방법은 데이터가 `null` (로드 중) 인지 여부를 추적 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-238">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="42051-239">기본 `Counter` 구성 요소에서 카운트는 `int`에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-239">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="42051-240">형식 (`int`)에 물음표 (`?`)를 추가 하 여 `currentCount` nullable로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-240">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="42051-241">개수 및 **증가값** 단추를 무조건 표시 하는 대신 데이터가 로드 된 경우에만 이러한 요소를 표시 하도록 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-241">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="42051-242">핸들 렌더링</span><span class="sxs-lookup"><span data-stu-id="42051-242">Handle prerendering</span></span>

<span data-ttu-id="42051-243">렌더링 하는 동안:</span><span class="sxs-lookup"><span data-stu-id="42051-243">During prerendering:</span></span>

* <span data-ttu-id="42051-244">사용자의 브라우저에 대 한 대화형 연결이 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-244">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="42051-245">브라우저에 JavaScript 코드를 실행할 수 있는 페이지가 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-245">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="42051-246">`localStorage` 또는 `sessionStorage`은 렌더링을 수행 하는 동안 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-246">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="42051-247">구성 요소가 저장소와 상호 작용 하려고 하면 다음과 유사한 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-247">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="42051-248">지금은 JavaScript interop 호출을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-248">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="42051-249">구성 요소가 미리 렌더링 된 되 고 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-249">This is because the component is being prerendered.</span></span>

<span data-ttu-id="42051-250">오류를 해결 하는 한 가지 방법은 사전 렌더링을 사용 하지 않도록 설정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-250">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="42051-251">일반적으로 앱이 브라우저 기반 저장소를 많이 사용 하는 경우이 옵션을 선택 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-251">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="42051-252">응용 프로그램은 `localStorage` 또는 `sessionStorage`을 사용할 수 있을 때까지 유용한 콘텐츠를 미리 만들 수 없기 때문에 응용 프로그램에서 복잡성을 더하고 앱을 활용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-252">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="42051-253">렌더링을 사용 하지 않도록 설정 하려면 *Pages/_Host cshtml* 파일을 열고 `Html.RenderComponentAsync<App>(RenderMode.Server)`호출을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-253">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `Html.RenderComponentAsync<App>(RenderMode.Server)`.</span></span>

<span data-ttu-id="42051-254">비 렌더링은 `localStorage` 또는 `sessionStorage`을 사용 하지 않는 다른 페이지에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-254">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="42051-255">렌더링을 사용 하도록 설정 된 상태로 유지 하려면 브라우저가 회로에 연결 될 때까지 로드 작업을 지연 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="42051-255">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="42051-256">카운터 값을 저장 하는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-256">The following is an example for storing a counter value:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="42051-257">일반 위치로 상태 유지의 비율을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-257">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="42051-258">많은 구성 요소가 브라우저 기반 저장소를 사용 하는 경우 상태 공급자 코드를 여러 번 다시 구현 하면 코드 중복이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-258">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="42051-259">코드 중복을 방지 하는 한 가지 옵션은 상태 공급자 논리를 캡슐화 하는 *상태 제공자 부모 구성 요소* 를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-259">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="42051-260">자식 구성 요소는 상태 지 속성 메커니즘에 관계 없이 지속형 데이터를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-260">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="42051-261">다음 `CounterStateProvider` 구성 요소 예제에서는 카운터 데이터가 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="42051-261">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="42051-262">`CounterStateProvider` 구성 요소는 로드가 완료 될 때까지 자식 콘텐츠를 렌더링 하지 않고 로드 단계를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-262">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="42051-263">`CounterStateProvider` 구성 요소를 사용 하려면 카운터 상태에 액세스 해야 하는 다른 구성 요소 주위에 구성 요소의 인스턴스를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-263">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="42051-264">앱의 모든 구성 요소에서 상태를 액세스할 수 있도록 하려면 `App` 구성 요소 (*응용 프로그램 razor*)의 `Router` 주위에 `CounterStateProvider` 구성 요소를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-264">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="42051-265">래핑된 구성 요소는 지속형 카운터 상태를 수신 하 고 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-265">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="42051-266">다음 `Counter` 구성 요소는 패턴을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-266">The following `Counter` component implements the pattern:</span></span>

```cshtml
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

<span data-ttu-id="42051-267">이전 구성 요소는 `ProtectedBrowserStorage`과 상호 작용 하는 데 필요 하지 않으며 "로드 중" 단계를 처리 하지도 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-267">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="42051-268">앞에서 설명한 대로 미리 렌더링을 처리 하기 위해 카운터 데이터를 사용 하는 모든 구성 요소가 자동 렌더링에서 자동으로 작동 하도록 `CounterStateProvider`을 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-268">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="42051-269">자세한 내용은 [렌더링 핸들 렌더링](#handle-prerendering) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="42051-269">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="42051-270">일반적으로 *상태 제공자 부모 구성 요소* 패턴을 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="42051-270">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="42051-271">다른 많은 구성 요소에서 상태를 사용 하려면</span><span class="sxs-lookup"><span data-stu-id="42051-271">To consume state in many other components.</span></span>
* <span data-ttu-id="42051-272">유지할 최상위 상태 개체가 하나만 있는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="42051-272">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="42051-273">여러 가지 상태 개체를 유지 하 고 다른 위치에서 개체의 다른 하위 집합을 사용 하려면 전역적으로 상태 로드 및 저장을 처리 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="42051-273">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
