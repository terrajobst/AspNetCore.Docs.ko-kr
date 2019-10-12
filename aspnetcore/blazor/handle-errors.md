---
title: ASP.NET Core Blazor apps의 오류 처리
author: guardrex
description: Blazor에서 처리 되지 않은 예외를 관리 하는 방법 및 오류를 검색 하 고 처리 하는 앱을 개발 하는 방법에 대해 ASP.NET Core 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/handle-errors
ms.openlocfilehash: fb4c7cacfe8be2417d6009cfc722595d0d91d530
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288840"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="4ca82-103">ASP.NET Core Blazor apps의 오류 처리</span><span class="sxs-lookup"><span data-stu-id="4ca82-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="4ca82-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="4ca82-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="4ca82-105">이 문서에서는 Blazor에서 처리 되지 않은 예외를 관리 하 고 오류를 검색 하 고 처리 하는 앱을 개발 하는 방법을 설명 합니다</span><span class="sxs-lookup"><span data-stu-id="4ca82-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a><span data-ttu-id="4ca82-106">Blazor framework가 처리 되지 않은 예외에 반응 하는 방법</span><span class="sxs-lookup"><span data-stu-id="4ca82-106">How the Blazor framework reacts to unhandled exceptions</span></span>

<span data-ttu-id="4ca82-107">Blazor 서버는 상태 저장 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-107">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="4ca82-108">사용자가 앱과 상호 작용 하는 동안 *회로*라는 서버에 대 한 연결을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-108">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="4ca82-109">회로는 활성 구성 요소 인스턴스를 비롯 하 여 상태에 대 한 다른 많은 측면을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-109">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="4ca82-110">구성 요소의 가장 최근 렌더링 된 출력입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-110">The most recent rendered output of components.</span></span>
* <span data-ttu-id="4ca82-111">클라이언트 쪽 이벤트에 의해 트리거될 수 있는 현재 이벤트 처리 대리자 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-111">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="4ca82-112">사용자가 여러 브라우저 탭에서 앱을 여는 경우 여러 개의 독립적인 회로가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-112">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="4ca82-113">Blazor는 발생 하는 대부분의 처리 되지 않은 예외를 심각 하 게 발생 하는 회로에 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-113">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="4ca82-114">처리 되지 않은 예외로 인해 회로가 종료 되는 경우 사용자는 페이지를 다시 로드 하 여 새 회로를 만들기만 하면 앱과 계속 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-114">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="4ca82-115">다른 사용자 또는 다른 브라우저 탭의 회로 인 종료 된 회로 외부의 회로는 영향을 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-115">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="4ca82-116">이 시나리오는 충돌 하는 앱을 다시 시작 해야 하지만 다른 앱은 영향을 받지 않는 데스크톱 앱과 유사 합니다.-0the.</span><span class="sxs-lookup"><span data-stu-id="4ca82-116">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="4ca82-117">다음과 같은 이유로 처리 되지 않은 예외가 발생 하면 회로가 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-117">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="4ca82-118">처리 되지 않은 예외는 종종 회로를 정의 되지 않은 상태로 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-118">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="4ca82-119">처리 되지 않은 예외가 발생 한 후에는 앱의 일반 작업을 보장할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-119">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="4ca82-120">회로를 계속 진행 하면 보안 취약성이 앱에 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-120">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="4ca82-121">개발자 코드에서 처리 되지 않은 예외 관리</span><span class="sxs-lookup"><span data-stu-id="4ca82-121">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="4ca82-122">오류가 발생 한 후에도 앱을 계속 하려면 앱에 오류 처리 논리가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-122">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="4ca82-123">이 문서의 이후 섹션에서는 처리 되지 않은 예외의 잠재적 원인을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-123">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="4ca82-124">프로덕션 환경에서는 UI에서 프레임 워크 예외 메시지 또는 스택 추적을 렌더링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-124">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="4ca82-125">예외 메시지 렌더링 또는 스택 추적:</span><span class="sxs-lookup"><span data-stu-id="4ca82-125">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="4ca82-126">최종 사용자에 게 중요 한 정보를 공개 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-126">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="4ca82-127">악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상 시킬 수 있는 앱의 약점을 검색 하도록 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-127">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="4ca82-128">영구 공급자를 사용 하 여 오류 기록</span><span class="sxs-lookup"><span data-stu-id="4ca82-128">Log errors with a persistent provider</span></span>

<span data-ttu-id="4ca82-129">처리 되지 않은 예외가 발생 하는 경우 예외는 서비스 컨테이너에 구성 된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-129">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="4ca82-130">기본적으로 Blazor apps는 콘솔 로깅 공급자를 사용 하 여 콘솔 출력에 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-130">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="4ca82-131">로그 크기와 로그 회전을 관리 하는 공급자를 사용 하 여 보다 영구적인 위치에 로깅하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-131">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="4ca82-132">자세한 내용은 <xref:fundamentals/logging/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ca82-132">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="4ca82-133">개발 하는 동안 Blazor는 일반적으로 디버깅에 도움이 되도록 브라우저 콘솔에 예외의 전체 세부 정보를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-133">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="4ca82-134">프로덕션에서 브라우저 콘솔의 자세한 오류는 기본적으로 사용 하지 않도록 설정 되어 있습니다. 즉, 오류가 클라이언트로 전송 되지 않지만 예외의 전체 세부 정보는 여전히 서버 쪽에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-134">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="4ca82-135">자세한 내용은 <xref:fundamentals/error-handling>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ca82-135">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="4ca82-136">로깅할 인시던트 및 로깅 된 인시던트의 심각도 수준을 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-136">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="4ca82-137">악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-137">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="4ca82-138">예를 들어 제품 세부 정보를 표시 하는 구성 요소의 URL에서 알 수 없는 `ProductId`이 제공 되는 오류에서 인시던트를 기록 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-138">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="4ca82-139">일부 오류는 로깅에 대 한 심각도가 높은 인시던트로 처리 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-139">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="4ca82-140">오류가 발생할 수 있는 위치</span><span class="sxs-lookup"><span data-stu-id="4ca82-140">Places where errors may occur</span></span>

<span data-ttu-id="4ca82-141">프레임 워크 및 앱 코드는 다음 위치에서 처리 되지 않은 예외를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-141">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="4ca82-142">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="4ca82-142">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="4ca82-143">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="4ca82-143">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="4ca82-144">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="4ca82-144">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="4ca82-145">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="4ca82-145">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="4ca82-146">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="4ca82-146">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="4ca82-147">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="4ca82-147">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="4ca82-148">회로 처리기</span><span class="sxs-lookup"><span data-stu-id="4ca82-148">Circuit handlers</span></span>](#circuit-handlers)
* [<span data-ttu-id="4ca82-149">회로 삭제</span><span class="sxs-lookup"><span data-stu-id="4ca82-149">Circuit disposal</span></span>](#circuit-disposal)
* [<span data-ttu-id="4ca82-150">사전</span><span class="sxs-lookup"><span data-stu-id="4ca82-150">Prerendering</span></span>](#prerendering)

<span data-ttu-id="4ca82-151">위의 처리 되지 않은 예외는이 문서의 다음 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-151">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="4ca82-152">구성 요소 인스턴스화</span><span class="sxs-lookup"><span data-stu-id="4ca82-152">Component instantiation</span></span>

<span data-ttu-id="4ca82-153">Blazor가 구성 요소의 인스턴스를 만들 때:</span><span class="sxs-lookup"><span data-stu-id="4ca82-153">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="4ca82-154">구성 요소의 생성자가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-154">The component's constructor is invoked.</span></span>
* <span data-ttu-id="4ca82-155">[@No__t-1](xref:blazor/dependency-injection#request-a-service-in-a-component) 지시문 또는 [[주입할]](xref:blazor/dependency-injection#request-a-service-in-a-component) 특성을 통해 구성 요소 생성자에 제공 된 단일 항목이 아닌 DI 서비스의 생성자가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-155">The constructors of any non-singleton DI services supplied to the component's constructor via the [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [[Inject]](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span> 

<span data-ttu-id="4ca82-156">모든 `[Inject]` 속성에 대해 실행 된 생성자 또는 setter가 처리 되지 않은 예외를 throw 하는 경우 회로는 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-156">A circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="4ca82-157">이 예외는 프레임 워크에서 구성 요소를 인스턴스화할 수 없기 때문에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-157">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="4ca82-158">생성자 논리에서 예외를 throw 할 수 있는 경우 앱은 오류 처리 및 로깅이 포함 된 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 하 여 예외를 트래핑 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-158">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="4ca82-159">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="4ca82-159">Lifecycle methods</span></span>

<span data-ttu-id="4ca82-160">구성 요소의 수명 동안 Blazor는 수명 주기 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-160">During the lifetime of a component, Blazor invokes lifecycle methods:</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="4ca82-161">수명 주기 메서드가 동기적 또는 비동기적으로 예외를 throw 하는 경우이는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-161">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to the circuit.</span></span> <span data-ttu-id="4ca82-162">구성 요소가 수명 주기 메서드에서 오류를 처리 하려면 오류 처리 논리를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-162">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="4ca82-163">다음 예제에서 `OnParametersSetAsync`은 메서드를 호출 하 여 제품을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-163">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="4ca82-164">@No__t-0 메서드에서 throw 된 예외는 `try-catch` 문에 의해 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-164">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="4ca82-165">@No__t-0 블록이 실행 될 때:</span><span class="sxs-lookup"><span data-stu-id="4ca82-165">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="4ca82-166">`loadFailed`은 사용자에 게 오류 메시지를 표시 하는 데 사용 되는 `true`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-166">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="4ca82-167">오류가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-167">The error is logged.</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="4ca82-168">렌더링 논리</span><span class="sxs-lookup"><span data-stu-id="4ca82-168">Rendering logic</span></span>

<span data-ttu-id="4ca82-169">@No__t-0 구성 요소 파일의 선언적 태그는 `BuildRenderTree` 라는 C# 메서드로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-169">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="4ca82-170">구성 요소가 렌더링 되는 경우 `BuildRenderTree`은 렌더링 된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명 하는 데이터 구조를 실행 하 고 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-170">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="4ca82-171">렌더링 논리는 예외를 throw 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-171">Rendering logic can throw an exception.</span></span> <span data-ttu-id="4ca82-172">이 시나리오의 예는 `@someObject.PropertyName`이 계산 되었지만 `@someObject`이 `null` 인 경우에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-172">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="4ca82-173">렌더링 논리에서 throw 된 처리 되지 않은 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-173">An unhandled exception thrown by rendering logic is fatal to the circuit.</span></span>

<span data-ttu-id="4ca82-174">렌더링 논리에서 null 참조 예외가 발생 하지 않도록 하려면 해당 멤버에 액세스 하기 전에 `null` 개체를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-174">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="4ca82-175">다음 예에서는 `person.Address`이 `null` 인 경우 `person.Address` 속성에 액세스 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-175">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="4ca82-176">위의 코드에서는 `person`이-1 @no__t 되지 않는다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-176">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="4ca82-177">일반적으로 코드 구조는 구성 요소가 렌더링 될 때 개체가 존재 하는 것을 보장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-177">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="4ca82-178">이러한 경우 렌더링 논리에서 `null`을 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-178">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="4ca82-179">이전 예제에서는 구성 요소가 인스턴스화될 때 `person`이 만들어지기 때문에 `person`이 존재 하는 것으로 보장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-179">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="4ca82-180">이벤트 처리기</span><span class="sxs-lookup"><span data-stu-id="4ca82-180">Event handlers</span></span>

<span data-ttu-id="4ca82-181">클라이언트 쪽 코드는를 사용 하 C# 여 이벤트 처리기를 만들 때 코드의 호출을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-181">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="4ca82-182">기타 `@on...` 특성</span><span class="sxs-lookup"><span data-stu-id="4ca82-182">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="4ca82-183">이벤트 처리기 코드는 이러한 시나리오에서 처리 되지 않은 예외를 throw 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-183">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="4ca82-184">이벤트 처리기가 처리 되지 않은 예외를 throw 하는 경우 (예: 데이터베이스 쿼리가 실패 하는 경우) 회로에 대 한 예외입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-184">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to the circuit.</span></span> <span data-ttu-id="4ca82-185">앱에서 외부 이유로 실패할 수 있는 코드를 호출 하는 경우 오류 처리 및 로깅이 포함 된 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 하 여 예외를 트래핑 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-185">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="4ca82-186">사용자 코드에서 예외를 트래핑 및 처리 하지 않는 경우 프레임 워크는 예외를 기록 하 고 회로를 종료 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-186">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="4ca82-187">구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="4ca82-187">Component disposal</span></span>

<span data-ttu-id="4ca82-188">예를 들어 사용자가 다른 페이지로 이동 했으므로 UI에서 구성 요소를 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-188">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="4ca82-189">@No__t를 구현 하는 구성 요소가 UI에서 제거 되 면 프레임 워크는 구성 요소의 <xref:System.IDisposable.Dispose*> 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-189">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span> 

<span data-ttu-id="4ca82-190">구성 요소의 `Dispose` 메서드가 처리 되지 않은 예외를 throw 하는 경우 해당 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-190">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="4ca82-191">삭제 논리에서 예외를 throw 할 수 있는 경우 앱은 오류 처리 및 로깅이 포함 된 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 하 여 예외를 트래핑 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-191">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="4ca82-192">구성 요소 삭제에 대 한 자세한 내용은 <xref:blazor/components#component-disposal-with-idisposable>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4ca82-192">For more information on component disposal, see <xref:blazor/components#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="4ca82-193">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="4ca82-193">JavaScript interop</span></span>

<span data-ttu-id="4ca82-194">`IJSRuntime.InvokeAsync<T>`을 사용 하면 .NET 코드에서 사용자 브라우저의 JavaScript 런타임에 대 한 비동기 호출을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-194">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="4ca82-195">다음 조건은 `InvokeAsync<T>`의 오류 처리에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-195">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="4ca82-196">@No__t-0에 대 한 호출이 동기적으로 실패 하면 .NET 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-196">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="4ca82-197">예를 들어 제공 된 인수를 직렬화 할 수 없기 때문에 `InvokeAsync<T>`에 대 한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-197">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="4ca82-198">개발자 코드는 예외를 catch 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-198">Developer code must catch the exception.</span></span> <span data-ttu-id="4ca82-199">이벤트 처리기 또는 구성 요소 수명 주기 메서드의 앱 코드에서 예외를 처리 하지 않으면 결과로 생성 되는 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-199">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to the circuit.</span></span>
* <span data-ttu-id="4ca82-200">@No__t-0에 대 한 호출이 비동기적으로 실패 하면 .NET <xref:System.Threading.Tasks.Task>이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-200">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="4ca82-201">예를 들어 `InvokeAsync<T>`에 대 한 호출이 실패할 수 있습니다. 예를 들어 JavaScript 쪽 코드에서 예외를 throw 하거나 `rejected`로 완료 된 `Promise`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-201">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="4ca82-202">개발자 코드는 예외를 catch 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-202">Developer code must catch the exception.</span></span> <span data-ttu-id="4ca82-203">[await](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용 하는 경우 오류 처리 및 로깅이 포함 된 [ try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-203">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="4ca82-204">그렇지 않으면 실패 한 코드는 회로에 치명적이 지 않은 처리 되지 않은 예외를 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-204">Otherwise, the failing code results in an unhandled exception that's fatal to the circuit.</span></span>
* <span data-ttu-id="4ca82-205">기본적으로 `InvokeAsync<T>`에 대 한 호출은 특정 기간 내에 완료 되어야 합니다. 그렇지 않으면 호출 시간이 초과 됩니다. 기본 제한 시간은 1 분입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-205">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="4ca82-206">제한 시간은 완료 메시지를 다시 전송 하지 않는 네트워크 연결 또는 JavaScript 코드의 손실에 대해 코드를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-206">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="4ca82-207">호출 시간이 초과 되는 경우 결과 `Task`이 <xref:System.OperationCanceledException>과 함께 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-207">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="4ca82-208">로깅을 사용 하 여 예외를 트래핑 하 고 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-208">Trap and process the exception with logging.</span></span>

<span data-ttu-id="4ca82-209">마찬가지로 JavaScript 코드는 [[JSInvokable] 특성](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)으로 표시 되는 .net 메서드에 대 한 호출을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-209">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [[JSInvokable] attribute](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions).</span></span> <span data-ttu-id="4ca82-210">이러한 .NET 메서드에서 처리 되지 않은 예외를 throw 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4ca82-210">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="4ca82-211">이 예외는 회로에 대 한 치명적으로 처리 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-211">The exception isn't treated as fatal to the circuit.</span></span>
* <span data-ttu-id="4ca82-212">JavaScript 쪽 `Promise`이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-212">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="4ca82-213">.NET 쪽 또는 메서드 호출의 JavaScript 쪽에서 오류 처리 코드를 사용 하는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-213">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="4ca82-214">자세한 내용은 <xref:blazor/javascript-interop>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ca82-214">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="circuit-handlers"></a><span data-ttu-id="4ca82-215">회로 처리기</span><span class="sxs-lookup"><span data-stu-id="4ca82-215">Circuit handlers</span></span>

<span data-ttu-id="4ca82-216">Blazor를 사용 하면 코드에서 사용자 회로의 상태가 변경 될 때 알림을 수신 하는 *회로 처리기*를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-216">Blazor allows code to define a *circuit handler*, which receives notifications when the state of a user's circuit changes.</span></span> <span data-ttu-id="4ca82-217">사용 되는 상태는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-217">The following states are used:</span></span>

* `initialized`
* `connected`
* `disconnected`
* `disposed`

<span data-ttu-id="4ca82-218">@No__t-0 추상 기본 클래스에서 상속 되는 DI 서비스를 등록 하 여 알림을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-218">Notifications are managed by registering a DI service that inherits from the `CircuitHandler` abstract base class.</span></span>

<span data-ttu-id="4ca82-219">사용자 지정 회로 처리기의 메서드에서 처리 되지 않은 예외를 throw 하는 경우 해당 예외는 회로에 치명적입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-219">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="4ca82-220">처리기의 코드 또는 호출 된 메서드에서 예외를 허용 하려면 오류 처리 및 로깅을 사용 하 여 하나 이상의 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문에 코드를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-220">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="circuit-disposal"></a><span data-ttu-id="4ca82-221">회로 삭제</span><span class="sxs-lookup"><span data-stu-id="4ca82-221">Circuit disposal</span></span>

<span data-ttu-id="4ca82-222">사용자가 연결을 끊고 프레임 워크가 회로 상태를 정리 하기 때문에 회로가 종료 되 면 프레임 워크는 회로의 DI 범위를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-222">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="4ca82-223">범위를 삭제 하면 <xref:System.IDisposable?displayProperty=fullName>을 구현 하는 모든 회로 범위 DI 서비스가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-223">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="4ca82-224">삭제 하는 동안 DI 서비스에서 처리 되지 않은 예외를 throw 하는 경우 프레임 워크는 예외를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-224">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="prerendering"></a><span data-ttu-id="4ca82-225">사전</span><span class="sxs-lookup"><span data-stu-id="4ca82-225">Prerendering</span></span>

<span data-ttu-id="4ca82-226">Blazor 구성 요소는 `Html.RenderComponentAsync`을 사용 하 여 미리 렌더링 된 수 있으므로 렌더링 된 HTML 태그가 사용자의 초기 HTTP 요청 일부로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-226">Blazor components can be prerendered using `Html.RenderComponentAsync` so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="4ca82-227">다음 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-227">This works by:</span></span>

* <span data-ttu-id="4ca82-228">동일한 페이지의 일부인 모든 미리 렌더링 된 구성 요소를 포함 하는 새 회로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-228">Creating a new circuit containing all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="4ca82-229">초기 HTML을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-229">Generating the initial HTML.</span></span>
* <span data-ttu-id="4ca82-230">회로의 대화형 작업을 다시 시작 하기 위해 사용자의 브라우저가 동일한 서버에 SignalR 연결을 설정할 때까지 회로를 `disconnected`으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-230">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server to resume interactivity on the circuit.</span></span>

<span data-ttu-id="4ca82-231">예를 들어, 수명 주기 방법이 나 렌더링 논리에서 렌더링 하는 동안 구성 요소가 처리 되지 않은 예외를 throw 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="4ca82-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="4ca82-232">회로에 대 한 심각한 예외입니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="4ca82-233">예외는 `Html.RenderComponentAsync` 호출에서 호출 스택을 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-233">The exception is thrown up the call stack from the `Html.RenderComponentAsync` call.</span></span> <span data-ttu-id="4ca82-234">따라서 개발자 코드에서 예외를 명시적으로 catch 하지 않으면 전체 HTTP 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="4ca82-235">일반적인 경우에는 사전 렌더링에 실패 하는 경우 작업 구성 요소를 렌더링할 수 없기 때문에 구성 요소를 계속 빌드 및 렌더링 하는 것은 적합 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="4ca82-236">렌더링 중에 발생할 수 있는 오류를 허용 하려면 예외를 throw 할 수 있는 구성 요소 내부에 오류 처리 논리를 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="4ca82-237">오류 처리 및 로깅과 함께 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-237">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="4ca82-238">@No__t-1 문에서 `RenderComponentAsync`에 대 한 호출을 래핑하는 대신 `RenderComponentAsync`로 렌더링 된 구성 요소에 오류 처리 논리를 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-238">Instead of wrapping the call to `RenderComponentAsync` in a `try-catch` statement, place error handling logic in the component rendered by `RenderComponentAsync`.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="4ca82-239">고급 시나리오</span><span class="sxs-lookup"><span data-stu-id="4ca82-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="4ca82-240">재귀 렌더링</span><span class="sxs-lookup"><span data-stu-id="4ca82-240">Recursive rendering</span></span>

<span data-ttu-id="4ca82-241">구성 요소는 재귀적으로 중첩 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-241">Components can be nested recursively.</span></span> <span data-ttu-id="4ca82-242">이는 재귀 데이터 구조를 나타내는 데 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="4ca82-243">예를 들어 `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 `TreeNode` 개의 구성 요소를 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="4ca82-244">반복적으로 렌더링 하는 경우 무한 재귀가 발생 하는 코딩 패턴을 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="4ca82-245">주기가 포함 된 데이터 구조를 재귀적으로 렌더링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="4ca82-246">예를 들어 자식을 포함 하는 트리 노드를 렌더링 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="4ca82-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="4ca82-247">주기가 포함 된 레이아웃 체인을 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="4ca82-248">예를 들어 레이아웃이 자체 인 레이아웃은 만들지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="4ca82-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="4ca82-249">최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 (규칙)을 위반할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="4ca82-250">렌더링 중 무한 루프:</span><span class="sxs-lookup"><span data-stu-id="4ca82-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="4ca82-251">렌더링 프로세스가 계속 지속 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="4ca82-252">은 종결 되지 않은 루프를 만드는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="4ca82-253">이러한 시나리오에서 영향을 받는 회로는 중단 되 고, 스레드는 일반적으로 다음을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-253">In these scenarios, the affected circuit hangs, and the thread usually attempts to:</span></span>

* <span data-ttu-id="4ca82-254">운영 체제에서 허용 하는 만큼의 CPU 시간을 무기한으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="4ca82-255">서버 메모리를 무제한으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="4ca82-256">무제한 메모리를 사용 하는 것은 종결 되지 않은 루프가 반복 될 때마다 컬렉션에 항목을 추가 하는 시나리오와 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="4ca82-257">무한 재귀 패턴을 방지 하려면 재귀 렌더링 코드에 적절 한 중지 조건이 포함 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="4ca82-258">사용자 지정 렌더링 트리 논리</span><span class="sxs-lookup"><span data-stu-id="4ca82-258">Custom render tree logic</span></span>

<span data-ttu-id="4ca82-259">대부분의 Blazor 구성 요소는 *razor* 파일로 구현 되 고 `RenderTreeBuilder`에서 작동 하 여 출력을 렌더링 하는 논리를 생성 하도록 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-259">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="4ca82-260">개발자는 절차적 C# 코드를 사용 하 여 `RenderTreeBuilder` 논리를 수동으로 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-260">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="4ca82-261">자세한 내용은 <xref:blazor/components#manual-rendertreebuilder-logic>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ca82-261">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="4ca82-262">수동 렌더링 트리 작성기 논리를 사용 하는 것은 일반적인 구성 요소 개발에는 권장 되지 않는 고급 및 안전 하지 않은 시나리오로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="4ca82-263">@No__t-0 코드를 작성 하는 경우 개발자는 코드의 정확성을 보장 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-263">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="4ca82-264">예를 들어 개발자는 다음을 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="4ca82-265">@No__t-0 및 `CloseElement`에 대 한 호출은 올바르게 균형이 조정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-265">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="4ca82-266">특성은 올바른 위치에만 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="4ca82-267">잘못 된 수동 렌더링 트리 작성기 논리가 있으면 충돌, 서버 중단 및 보안 취약성을 포함 하 여 정의 되지 않은 임의의 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="4ca82-268">어셈블리 코드 또는 MSIL 명령을 직접 작성 하 *는 것과 동일한 수준의* 복잡성과 동일한 수준의 복잡성을 가진 수동 렌더링 트리 작성기 논리를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ca82-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
