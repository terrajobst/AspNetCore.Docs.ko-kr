---
title: ASP.NET Core Blazor 수명 주기
author: guardrex
description: ASP.NET Core Blazor 앱에서 Razor 구성 요소 수명 주기 메서드를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647583"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor 수명 주기

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

Blazor 프레임워크는 동기 및 비동기 수명 주기 메서드를 포함합니다. 구성 요소 초기화 및 렌더링 중에 구성 요소에서 추가 작업을 수행하려면 수명 주기 메서드를 재정의합니다.

## <a name="lifecycle-methods"></a>수명 주기 메서드

### <a name="component-initialization-methods"></a>구성 요소 초기화 메서드

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*>는 구성 요소가 부모 구성 요소에서 초기 매개 변수를 받은 후 초기화할 때 호출됩니다. 구성 요소가 비동기 작업을 수행하며, 작업이 완료될 때 새로 고쳐야 하는 경우 `OnInitializedAsync`를 사용합니다.

동기 작업의 경우 `OnInitialized`를 재정의합니다.

```csharp
protected override void OnInitialized()
{
    ...
}
```

비동기 작업을 수행하려면 `OnInitializedAsync`를 재정의하고, 작업에 `await` 키워드를 사용합니다.

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

[콘텐츠를 미리 렌더링](xref:blazor/hosting-model-configuration#render-mode)하는 Blazor 서버 앱은 `OnInitializedAsync`를 **두 번** 호출합니다. 

* 첫 번째 호출: 구성 요소가 처음에 페이지 일부로 정적 렌더링될 때
* 두 번째 호출: 브라우저가 서버에 다시 연결할 때

`OnInitializedAsync`의 개발자 코드가 두 번 실행되는 것을 방지하려면 [미리 렌더링한 후의 상태 저장 다시 연결](#stateful-reconnection-after-prerendering) 섹션을 참조하세요.

Blazor 서버 앱을 미리 렌더링 중이면 브라우저에 연결되어 있지 않으므로 JavaScript 호출 등의 특정 작업을 수행할 수 없습니다. 미리 렌더링된 경우, 구성 요소를 다르게 렌더링해야 할 수도 있습니다. 자세한 내용은 [앱을 미리 렌더링 중인 경우 검색](#detect-when-the-app-is-prerendering) 섹션을 참조하세요.

### <a name="before-parameters-are-set"></a>매개 변수를 설정하기 전

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>는 렌더링 트리에서 구성 요소의 부모가 제공하는 매개 변수를 설정합니다.

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

`SetParametersAsync`를 호출할 때마다 <xref:Microsoft.AspNetCore.Components.ParameterView>에 매개 변수 값의 전체 집합이 포함됩니다.

`SetParametersAsync`의 기본 구현에서는 `ParameterView`에 해당 값이 있는 `[Parameter]` 또는 `[CascadingParameter]` 특성을 사용하여 각 속성의 값을 설정합니다. `ParameterView`에 해당 값이 없는 매개 변수는 변경되지 않고 그대로 유지됩니다.

`base.SetParametersAync`를 호출하지 않은 경우, 사용자 지정 코드는 들어오는 매개 변수 값을 필요한 방식으로 해석할 수 있습니다. 예를 들어 들어오는 매개 변수를 클래스의 속성에 할당해야 하는 요구 사항이 없습니다.

### <a name="after-parameters-are-set"></a>매개 변수를 설정한 후

다음 두 가지 경우에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>가 호출됩니다.

* 구성 요소가 초기화되고 부모 구성 요소에서 첫 번째 매개 변수 집합을 받은 경우
* 부모 구성 요소가 다시 렌더링되고 다음을 제공하는 경우
  * 하나 이상의 매개 변수가 변경된, 알려진 변경 불가능 기본 형식만
  * 복합 형식 매개 변수. 프레임워크는 복합 형식 매개 변수의 값이 내부적으로 변경되었는지 아닌지를 알 수 없으므로 매개 변수 집합을 변경된 것으로 처리합니다.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> 매개 변수와 속성 값을 적용하는 경우 비동기 작업은 `OnParametersSetAsync` 수명 주기 이벤트 중에 수행해야 합니다.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>구성 요소 렌더링 후

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*>는 구성 요소 렌더링을 완료한 후에 호출됩니다. 이때 요소 및 구성 요소 참조가 채워집니다. 렌더링된 DOM 요소에서 작동하는 타사 JavaScript 라이브러리 활성화와 같은 추가 초기화 단계를 렌더링된 콘텐츠로 수행하려면 이 단계를 사용합니다.

`OnAfterRenderAsync` 및 `OnAfterRender`의 `firstRender` 매개 변수와 관련해서 다음 사항을 확인합니다.

* 구성 요소 인스턴스를 처음 렌더링할 때는 `true`로 설정됩니다.
* 초기화 작업이 한 번만 수행되도록 하는 데 사용할 수 있습니다.

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
> 렌더링 직후의 비동기 작업은 `OnAfterRenderAsync` 수명 주기 이벤트 중에 수행해야 합니다.
>
> `OnAfterRenderAsync`에서 <xref:System.Threading.Tasks.Task>를 반환하는 경우에도, 해당 작업이 완료된 후에는 프레임워크에서 구성 요소에 대해 추가 렌더링 주기를 예약하지 않습니다. 이 동작은 무한 렌더링 루프를 방지하기 위한 것입니다. 반환된 작업이 완료된 후 추가 렌더링 주기를 예약하는 기타 수명 주기 메서드와는 다릅니다.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` 및 `OnAfterRenderAsync`‘는 서버에서 미리 렌더링되는 경우 호출되지 않습니다.’ 

### <a name="suppress-ui-refreshing"></a>UI 새로 고침 표시 안 함

UI 새로 고침을 표시하지 않으려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>를 재정의합니다. 구현에서 `true`를 반환하는 경우 UI가 새로 고쳐집니다.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

구성 요소를 렌더링할 때마다 `ShouldRender`가 호출됩니다.

`ShouldRender`를 재정의한 경우에도 처음에는 구성 요소가 항상 렌더링됩니다.

## <a name="state-changes"></a>상태 변경

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>는 구성 요소에 상태가 변경되었음을 알립니다. 해당하는 경우, `StateHasChanged`를 호출하면 구성 요소가 다시 렌더링됩니다.

## <a name="handle-incomplete-async-actions-at-render"></a>렌더링 시 불완전한 비동기 작업 처리

수명 주기 이벤트에서 수행한 비동기 작업이 구성 요소를 렌더링하기 전에 완료되지 않았을 수 있습니다. 수명 주기 메서드를 실행하는 동안 개체가 `null`이거나 불완전하게 데이터로 채워졌을 수 있습니다. 개체가 초기화되었는지 확인하는 렌더링 논리를 제공합니다. 개체가 `null`인 동안 자리 표시자 UI 요소(예: 로드 메시지)를 렌더링합니다.

Blazor 템플릿의 `FetchData` 구성 요소에서 `OnInitializedAsync`는 예측 데이터(`forecasts`)를 비동기적으로 수신하도록 재정의되었습니다. `forecasts`가 `null`인 경우 사용자에게 로드 메시지가 표시됩니다. `OnInitializedAsync`에서 반환된 `Task`가 완료되면 구성 요소가 업데이트된 상태로 다시 렌더링됩니다.

Blazor 서버 템플릿의 *Pages/FetchData.razor*:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>IDisposable을 사용한 구성 요소 삭제

구성 요소가 <xref:System.IDisposable>을 구현하는 경우, UI에서 구성 요소를 제거할 때 [Dispose 메서드](/dotnet/standard/garbage-collection/implementing-dispose)가 호출됩니다. 다음 구성 요소는 `@implements IDisposable` 및 `Dispose` 메서드를 사용합니다.

```razor
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
> `Dispose`에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> 호출은 지원되지 않습니다. 렌더러를 삭제하는 과정에서 `StateHasChanged`가 호출될 수 있으므로, 해당 시점에 UI 업데이트를 요청할 수는 없습니다.

## <a name="handle-errors"></a>오류 처리

수명 주기 메서드 실행 중의 오류 처리 방법에 대한 자세한 내용은 <xref:blazor/handle-errors#lifecycle-methods>를 참조하세요.

## <a name="stateful-reconnection-after-prerendering"></a>미리 렌더링 후의 상태 저장 다시 연결

Blazor 서버 앱에서 `RenderMode`가 `ServerPrerendered`인 경우, 구성 요소는 처음에 페이지 일부로 정적 렌더링됩니다. 브라우저가 서버에 다시 연결하면 구성 요소가 ‘다시’ 렌더링되고, 이제 대화형 구성 요소가 됩니다.  구성 요소를 초기화하기 위한 [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) 수명 주기 메서드가 있는 경우 메서드가 다음과 같이 ‘두 번’ 실행됩니다. 

* 구성 요소를 정적으로 미리 렌더링할 때
* 서버 연결이 설정된 후

이 동작 때문에 구성 요소를 최종적으로 렌더링할 때는 UI에 표시되는 데이터가 상당히 변경될 수 있습니다.

Blazor 서버 앱에서 이중 렌더링 시나리오를 방지하려면 다음을 수행합니다.

* 미리 렌더링 중에 상태를 캐시하고 앱이 다시 시작된 후 상태를 검색하는 데 사용할 수 있는 식별자를 전달합니다.
* 미리 렌더링 중에 식별자를 사용하여 구성 요소 상태를 저장합니다.
* 미리 렌더링 후에 식별자를 사용하여 캐시된 상태를 검색합니다.

다음 코드는 템플릿 기반 Blazor 서버 앱에서 이중 렌더링을 방지하도록 업데이트된 `WeatherForecastService`를 보여 줍니다.

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

`RenderMode`에 대한 자세한 내용은 <xref:blazor/hosting-model-configuration#render-mode>를 참조하세요.

## <a name="detect-when-the-app-is-prerendering"></a>앱을 미리 렌더링 중인 경우 검색

[!INCLUDE[](~/includes/blazor-prerendering.md)]
