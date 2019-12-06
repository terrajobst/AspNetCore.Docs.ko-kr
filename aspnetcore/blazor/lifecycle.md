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
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor 수명 주기

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

Blazor 프레임 워크는 동기 및 비동기 수명 주기 메서드를 포함 합니다. 구성 요소 초기화 및 렌더링 중에 구성 요소에서 추가 작업을 수행 하는 수명 주기 메서드를 재정의 합니다.

## <a name="lifecycle-methods"></a>수명 주기 메서드

### <a name="component-initialization-methods"></a>구성 요소 초기화 메서드

구성 요소를 초기화 하는 코드를 실행 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 합니다. 이러한 메서드는 구성 요소가 처음 인스턴스화될 때 한 번만 호출 됩니다.

비동기 작업을 수행 하려면 작업에서 `OnInitializedAsync` 및 `await` 키워드를 사용 합니다.

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> 구성 요소 초기화 중 비동기 작업은 `OnInitializedAsync` 수명 주기 이벤트 중에 발생 해야 합니다.

동기 작업의 경우 `OnInitialized`를 사용 합니다.

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a>매개 변수가 설정 되기 전에

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>는 렌더링 트리에서 구성 요소의 부모에 의해 제공 되는 매개 변수를 설정 합니다.

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>은 `SetParametersAsync` 호출 될 때마다 매개 변수 값의 전체 집합을 포함 합니다.

`SetParametersAsync`의 기본 구현에서는 각 속성의 값을 `ParameterView`에 해당 하는 값이 있는 `[Parameter]` 또는 `[CascadingParameter]` 특성으로 설정 합니다. `ParameterView`에 해당 값이 없는 매개 변수는 변경 되지 않은 상태로 유지 됩니다.

`base.SetParametersAync`를 호출 하지 않으면 사용자 지정 코드는 필요한 방식으로 들어오는 매개 변수 값을 해석할 수 있습니다. 예를 들어 클래스의 속성에 들어오는 매개 변수를 할당할 필요는 없습니다.

### <a name="after-parameters-are-set"></a>매개 변수가 설정 된 후

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>는 구성 요소가 부모 로부터 매개 변수를 수신 하 고 값이 속성에 할당 될 때 호출 됩니다. 이러한 메서드는 구성 요소 초기화 후와 새 매개 변수 값이 지정 될 때마다 실행 됩니다.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> 매개 변수 및 속성 값을 적용할 때 비동기 작업은 `OnParametersSetAsync` 수명 주기 이벤트 중에 발생 해야 합니다.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>구성 요소 렌더링 후

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>는 구성 요소가 렌더링을 완료 한 후에 호출 됩니다. 요소 및 구성 요소 참조가이 시점에 채워집니다. 렌더링 된 DOM 요소에 대해 작동 하는 타사 JavaScript 라이브러리 활성화와 같이 렌더링 된 콘텐츠를 사용 하 여 추가 초기화 단계를 수행 하려면이 단계를 사용 합니다.

`OnAfterRenderAsync` 및 `OnAfterRender`에 대 한 `firstRender` 매개 변수는 다음과 같습니다.

* 는 구성 요소 인스턴스를 처음 렌더링할 때 `true`로 설정 됩니다.
* 를 사용 하 여 초기화 작업이 한 번만 수행 되도록 할 수 있습니다.

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
> 렌더링 직후 비동기 작업은 `OnAfterRenderAsync` 수명 주기 이벤트 중에 발생 해야 합니다.
>
> `OnAfterRenderAsync`에서 <xref:System.Threading.Tasks.Task>을 반환 하는 경우에도 프레임 워크는 해당 작업이 완료 되 면 구성 요소에 대 한 추가 렌더링 주기를 예약 하지 않습니다. 무한 렌더링 루프를 방지 하기 위한 것입니다. 반환 된 작업이 완료 되 면 추가 렌더링 주기를 예약 하는 다른 수명 주기 방법과는 다릅니다.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` 및 `OnAfterRenderAsync`는 *서버에서 사전 렌더링 시 호출 되지 않습니다.*

### <a name="suppress-ui-refreshing"></a>UI 새로 고침 표시 안 함

UI 새로 고침을 표시 하지 않으려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>를 재정의 합니다. 구현에서 `true`반환 하는 경우 UI가 새로 고쳐집니다.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`는 구성 요소가 렌더링 될 때마다 호출 됩니다.

`ShouldRender`를 재정의 하더라도 구성 요소가 항상 처음에 렌더링 됩니다.

## <a name="state-changes"></a>상태 변경

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>는 구성 요소에 상태가 변경 되었음을 알립니다. 해당 하는 경우 `StateHasChanged`를 호출 하면 구성 요소에 대 한 필수 구성 요소가 됩니다.

## <a name="handle-incomplete-async-actions-at-render"></a>렌더링 시 불완전 한 비동기 작업 처리

수명 주기 이벤트에서 수행 된 비동기 작업은 구성 요소가 렌더링 되기 전에 완료 되지 않았을 수 있습니다. 수명 주기 메서드를 실행 하는 동안 개체가 데이터와 `null` 되거나 불완전 하 게 채워질 수 있습니다. 개체가 초기화 되었는지 확인 하는 렌더링 논리를 제공 합니다. 개체를 `null`하는 동안 자리 표시자 UI 요소 (예: 로드 메시지)를 렌더링 합니다.

Blazor 템플릿의 `FetchData` 구성 요소에서 `OnInitializedAsync`은 비동기적 receive 예측 데이터 (`forecasts`)로 재정의 됩니다. `forecasts` `null`되 면 로드 메시지가 사용자에 게 표시 됩니다. `OnInitializedAsync`에서 반환 된 `Task` 완료 되 면 구성 요소는 업데이트 된 상태를 사용 하 여 수행 됩니다.

Blazor 서버 템플릿의 *Pages/FetchData. razor* :

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>IDisposable을 사용한 구성 요소 삭제

구성 요소가 <xref:System.IDisposable>를 구현 하는 경우 UI에서 구성 요소가 제거 되 면 [Dispose 메서드가](/dotnet/standard/garbage-collection/implementing-dispose) 호출 됩니다. 다음 구성 요소는 `@implements IDisposable` 및 `Dispose` 메서드를 사용 합니다.

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
> `Dispose`에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>를 호출 하는 것은 지원 되지 않습니다. 렌더러를 종료 하는 과정의 일부로 호출 될 수도 있으므로 해당 지점에서 UI 업데이트를 요청 하는 것은 지원 되지 않습니다. `StateHasChanged`

## <a name="handle-errors"></a>오류 처리

수명 주기 메서드 실행 중 오류를 처리 하는 방법에 대 한 자세한 내용은 <xref:blazor/handle-errors#lifecycle-methods>를 참조 하세요.
