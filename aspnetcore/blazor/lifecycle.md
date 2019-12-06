---
title: ASP.NET Core Blazor 수명 주기
author: guardrex
description: ASP.NET Core Blazor 앱에서 Razor 구성 요소 수명 주기 메서드를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: 280ea832f492852e425e3e15c61cac54fd1e39d6
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879677"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="58d16-103">ASP.NET Core Blazor 수명 주기</span><span class="sxs-lookup"><span data-stu-id="58d16-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="58d16-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="58d16-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="58d16-105">Blazor 프레임 워크는 동기 및 비동기 수명 주기 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="58d16-106">구성 요소 초기화 및 렌더링 중에 구성 요소에서 추가 작업을 수행 하는 수명 주기 메서드를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="58d16-107">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="58d16-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="58d16-108">구성 요소 초기화 메서드</span><span class="sxs-lookup"><span data-stu-id="58d16-108">Component initialization methods</span></span>

<span data-ttu-id="58d16-109">구성 요소를 초기화 하는 코드를 실행 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> execute code that initializes a component.</span></span> <span data-ttu-id="58d16-110">이러한 메서드는 구성 요소가 처음 인스턴스화될 때 한 번만 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-110">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="58d16-111">비동기 작업을 수행 하려면 작업에서 `OnInitializedAsync` 및 `await` 키워드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-111">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="58d16-112">구성 요소 초기화 중 비동기 작업은 `OnInitializedAsync` 수명 주기 이벤트 중에 발생 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-112">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="58d16-113">동기 작업의 경우 `OnInitialized`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-113">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="58d16-114">매개 변수가 설정 되기 전에</span><span class="sxs-lookup"><span data-stu-id="58d16-114">Before parameters are set</span></span>

<span data-ttu-id="58d16-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>는 렌더링 트리에서 구성 요소의 부모에 의해 제공 되는 매개 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="58d16-116"><xref:Microsoft.AspNetCore.Components.ParameterView>은 `SetParametersAsync` 호출 될 때마다 매개 변수 값의 전체 집합을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="58d16-117">`SetParametersAsync`의 기본 구현에서는 각 속성의 값을 `ParameterView`에 해당 하는 값이 있는 `[Parameter]` 또는 `[CascadingParameter]` 특성으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="58d16-118">`ParameterView`에 해당 값이 없는 매개 변수는 변경 되지 않은 상태로 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="58d16-119">`base.SetParametersAync`를 호출 하지 않으면 사용자 지정 코드는 필요한 방식으로 들어오는 매개 변수 값을 해석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="58d16-120">예를 들어 클래스의 속성에 들어오는 매개 변수를 할당할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="58d16-121">매개 변수가 설정 된 후</span><span class="sxs-lookup"><span data-stu-id="58d16-121">After parameters are set</span></span>

<span data-ttu-id="58d16-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>는 구성 요소가 부모 로부터 매개 변수를 수신 하 고 값이 속성에 할당 될 때 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="58d16-123">이러한 메서드는 구성 요소 초기화 후와 새 매개 변수 값이 지정 될 때마다 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-123">These methods are executed after component initialization and each time new parameter values are specified:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="58d16-124">매개 변수 및 속성 값을 적용할 때 비동기 작업은 `OnParametersSetAsync` 수명 주기 이벤트 중에 발생 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-124">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="58d16-125">구성 요소 렌더링 후</span><span class="sxs-lookup"><span data-stu-id="58d16-125">After component render</span></span>

<span data-ttu-id="58d16-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>는 구성 요소가 렌더링을 완료 한 후에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="58d16-127">요소 및 구성 요소 참조가이 시점에 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-127">Element and component references are populated at this point.</span></span> <span data-ttu-id="58d16-128">렌더링 된 DOM 요소에 대해 작동 하는 타사 JavaScript 라이브러리 활성화와 같이 렌더링 된 콘텐츠를 사용 하 여 추가 초기화 단계를 수행 하려면이 단계를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-128">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="58d16-129">`OnAfterRenderAsync` 및 `OnAfterRender`에 대 한 `firstRender` 매개 변수는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-129">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="58d16-130">는 구성 요소 인스턴스를 처음 렌더링할 때 `true`로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-130">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="58d16-131">를 사용 하 여 초기화 작업이 한 번만 수행 되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-131">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="58d16-132">렌더링 직후 비동기 작업은 `OnAfterRenderAsync` 수명 주기 이벤트 중에 발생 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-132">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="58d16-133">`OnAfterRenderAsync`에서 <xref:System.Threading.Tasks.Task>을 반환 하는 경우에도 프레임 워크는 해당 작업이 완료 되 면 구성 요소에 대 한 추가 렌더링 주기를 예약 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-133">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="58d16-134">무한 렌더링 루프를 방지 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-134">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="58d16-135">반환 된 작업이 완료 되 면 추가 렌더링 주기를 예약 하는 다른 수명 주기 방법과는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-135">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="58d16-136">`OnAfterRender` 및 `OnAfterRenderAsync`는 *서버에서 사전 렌더링 시 호출 되지 않습니다.*</span><span class="sxs-lookup"><span data-stu-id="58d16-136">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="58d16-137">UI 새로 고침 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="58d16-137">Suppress UI refreshing</span></span>

<span data-ttu-id="58d16-138">UI 새로 고침을 표시 하지 않으려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-138">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="58d16-139">구현에서 `true`반환 하는 경우 UI가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-139">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="58d16-140">`ShouldRender`는 구성 요소가 렌더링 될 때마다 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-140">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="58d16-141">`ShouldRender`를 재정의 하더라도 구성 요소가 항상 처음에 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-141">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="58d16-142">상태 변경</span><span class="sxs-lookup"><span data-stu-id="58d16-142">State changes</span></span>

<span data-ttu-id="58d16-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>는 구성 요소에 상태가 변경 되었음을 알립니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="58d16-144">해당 하는 경우 `StateHasChanged`를 호출 하면 구성 요소에 대 한 필수 구성 요소가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-144">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="58d16-145">렌더링 시 불완전 한 비동기 작업 처리</span><span class="sxs-lookup"><span data-stu-id="58d16-145">Handle incomplete async actions at render</span></span>

<span data-ttu-id="58d16-146">수명 주기 이벤트에서 수행 된 비동기 작업은 구성 요소가 렌더링 되기 전에 완료 되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-146">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="58d16-147">수명 주기 메서드를 실행 하는 동안 개체가 데이터와 `null` 되거나 불완전 하 게 채워질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-147">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="58d16-148">개체가 초기화 되었는지 확인 하는 렌더링 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-148">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="58d16-149">개체를 `null`하는 동안 자리 표시자 UI 요소 (예: 로드 메시지)를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-149">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="58d16-150">Blazor 템플릿의 `FetchData` 구성 요소에서 `OnInitializedAsync`은 비동기적 receive 예측 데이터 (`forecasts`)로 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-150">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="58d16-151">`forecasts` `null`되 면 로드 메시지가 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-151">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="58d16-152">`OnInitializedAsync`에서 반환 된 `Task` 완료 되 면 구성 요소는 업데이트 된 상태를 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-152">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="58d16-153">Blazor 서버 템플릿의 *Pages/FetchData. razor* :</span><span class="sxs-lookup"><span data-stu-id="58d16-153">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="58d16-154">IDisposable을 사용한 구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="58d16-154">Component disposal with IDisposable</span></span>

<span data-ttu-id="58d16-155">구성 요소가 <xref:System.IDisposable>를 구현 하는 경우 UI에서 구성 요소가 제거 되 면 [Dispose 메서드가](/dotnet/standard/garbage-collection/implementing-dispose) 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-155">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="58d16-156">다음 구성 요소는 `@implements IDisposable` 및 `Dispose` 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-156">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="58d16-157">`Dispose`에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>를 호출 하는 것은 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d16-157">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="58d16-158">렌더러를 종료 하는 과정의 일부로 호출 될 수도 있으므로 해당 지점에서 UI 업데이트를 요청 하는 것은 지원 되지 않습니다. `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="58d16-158">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="58d16-159">오류 처리</span><span class="sxs-lookup"><span data-stu-id="58d16-159">Handle errors</span></span>

<span data-ttu-id="58d16-160">수명 주기 메서드 실행 중 오류를 처리 하는 방법에 대 한 자세한 내용은 <xref:blazor/handle-errors#lifecycle-methods>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58d16-160">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
