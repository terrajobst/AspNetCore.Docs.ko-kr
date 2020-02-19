---
title: ASP.NET Core Blazor 데이터 바인딩
author: guardrex
description: Blazor apps의 구성 요소 및 DOM 요소에 대 한 데이터 바인딩 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: c38e6095d4e93d3eead10fa8bb0356b2113bb220
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453159"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="91564-103">ASP.NET Core Blazor 데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="91564-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="91564-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="91564-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="91564-105">구성 요소와 DOM 요소 모두에 대 한 데이터 바인딩은 [`@bind`](xref:mvc/views/razor#bind) 특성을 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-105">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="91564-106">다음 예제에서는 `CurrentValue` 속성을 텍스트 상자의 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-106">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="91564-107">텍스트 상자가 포커스를 잃으면 속성의 값이 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="91564-108">텍스트 상자는 구성 요소가 렌더링 되는 경우에만 UI에서 업데이트 되며 속성의 값을 변경 하는 것에 대 한 응답으로는 업데이트 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="91564-109">이벤트 처리기 코드를 실행 한 후 구성 요소를 자체적으로 렌더링 하므로 속성 업데이트는 *일반적으로* 이벤트 처리기가 트리거되는 즉시 UI에 반영 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="91564-110">`CurrentValue` 속성 (`<input @bind="CurrentValue" />`)과 함께 `@bind`를 사용 하는 것은 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="91564-111">구성 요소가 렌더링 되 면 input 요소의 `value` `CurrentValue` 속성에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="91564-112">사용자가 텍스트 상자에를 입력 하 고 요소 포커스를 변경 하면 `onchange` 이벤트가 발생 하 고 `CurrentValue` 속성이 변경 된 값으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="91564-113">실제로는 `@bind`에서 형식 변환이 수행 되는 경우를 처리 하므로 코드 생성은 더 복잡 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="91564-114">원칙적으로 `@bind`은 식의 현재 값을 `value` 특성과 연결 하 고 등록 된 처리기를 사용 하 여 변경 내용을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="91564-115">`@bind` 구문을 사용 하 여 `onchange` 이벤트를 처리 하는 것 외에도 `event` 매개 변수 ([`@bind-value:event`](xref:mvc/views/razor#bind))를 사용 하 여 [`@bind-value`](xref:mvc/views/razor#bind) 특성을 지정 하 여 다른 이벤트를 사용 하 여 속성 또는 필드를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-115">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="91564-116">다음 예에서는 `oninput` 이벤트의 `CurrentValue` 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-116">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="91564-117">요소가 포커스를 잃을 때 발생 하는 `onchange`와는 달리 텍스트 상자의 값이 변경 될 때 발생 `oninput`.</span><span class="sxs-lookup"><span data-stu-id="91564-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="91564-118">이전 예제 바인딩의 `@bind-value`:</span><span class="sxs-lookup"><span data-stu-id="91564-118">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="91564-119">요소의 `value` 특성에 대 한 지정 된 식 (`CurrentValue`)입니다.</span><span class="sxs-lookup"><span data-stu-id="91564-119">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="91564-120">`@bind-value:event`으로 지정 된 이벤트에 대 한 변경 이벤트 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="91564-120">A change event delegate to the event specified by `@bind-value:event`.</span></span>

### <a name="unparsable-values"></a><span data-ttu-id="91564-121">구문 분석할 값</span><span class="sxs-lookup"><span data-stu-id="91564-121">Unparsable values</span></span>

<span data-ttu-id="91564-122">사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공 하면 bind 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="91564-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="91564-123">다음과 같은 시나리오를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="91564-123">Consider the following scenario:</span></span>

* <span data-ttu-id="91564-124">`<input>` 요소는 `123`초기 값을 사용 하 여 `int` 형식에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="91564-125">사용자는 요소의 값을 업데이트 하 여 페이지에서 `123.45` 하 고 요소 포커스를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="91564-126">위의 시나리오에서 요소의 값은 `123`로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="91564-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="91564-127">`123`의 원래 값을 사용 하 여 `123.45` 값이 거부 되 면 사용자는 해당 값이 허용 되지 않는다는 것을 이해 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="91564-128">기본적으로 바인딩은 요소의 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="91564-129">`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`를 사용 하 여 다른 이벤트를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-129">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="91564-130">`oninput` 이벤트 (`@bind-value:event="oninput"`)의 경우 변경할지는 구문 분석할 값을 제공 하는 키 입력 후에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-130">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="91564-131">`int`바인딩된 형식의 `oninput` 이벤트를 대상으로 지정 하는 경우 사용자는 `.` 문자를 입력할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="91564-132">`.` 문자는 즉시 제거 되므로 사용자는 전체 숫자만 허용 되는 즉각적인 피드백을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="91564-133">사용자가 구문 분석할 수 없는 `<input>` 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합 하지 않은 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="91564-134">대안은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-134">Alternatives include:</span></span>

* <span data-ttu-id="91564-135">`oninput` 이벤트를 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="91564-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="91564-136">요소가 포커스를 잃을 때까지 잘못 된 값이 되돌아가지 않는 기본 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-136">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="91564-137">`int?` 또는 `string`와 같은 nullable 형식에 바인딩하고 잘못 된 항목을 처리 하는 사용자 지정 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-137">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="91564-138">`InputNumber` 또는 `InputDate`와 같은 [폼 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-138">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="91564-139">양식 유효성 검사 구성 요소에는 잘못 된 입력을 관리 하기 위한 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="91564-140">양식 유효성 검사 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="91564-140">Form validation components:</span></span>
  * <span data-ttu-id="91564-141">사용자가 연결 된 `EditContext`에서 잘못 된 입력 및 수신 유효성 검사 오류를 제공할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-141">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="91564-142">사용자가 추가 webform 데이터를 입력 하는 것을 방해 하지 않고 UI에서 유효성 검사 오류를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-142">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

### <a name="format-strings"></a><span data-ttu-id="91564-143">서식 문자열</span><span class="sxs-lookup"><span data-stu-id="91564-143">Format strings</span></span>

<span data-ttu-id="91564-144">데이터 바인딩은 [`@bind:format`](xref:mvc/views/razor#bind)를 사용 하 여 <xref:System.DateTime> 형식 문자열과 함께 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-144">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="91564-145">통화 또는 숫자 형식과 같은 다른 형식 식은 현재 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-145">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="91564-146">위의 코드에서 `<input>` 요소의 필드 형식 (`type`)은 기본적으로 `text`입니다.</span><span class="sxs-lookup"><span data-stu-id="91564-146">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="91564-147">다음 .NET 형식의 바인딩에 대해 `@bind:format` 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-147">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="91564-148"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="91564-148"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="91564-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="91564-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="91564-150">`@bind:format` 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-150">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="91564-151">이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석 하는 데도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-151">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="91564-152">Blazor에서 날짜 형식을 기본적으로 지원 하기 때문에 `date` 필드 형식에 대 한 형식을 지정 하는 것은 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-152">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="91564-153">권장 사항에도 불구 하 고 `date` 필드 형식과 함께 형식이 제공 되는 경우 바인딩이 제대로 작동 하려면 `yyyy-MM-dd` 날짜 형식만 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-153">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="91564-154">구성 요소 매개 변수를 사용 하 여 부모-자식 바인딩</span><span class="sxs-lookup"><span data-stu-id="91564-154">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="91564-155">바인딩은 구성 요소 매개 변수를 인식 합니다. 여기서 `@bind-{property}`는 부모 구성 요소의 속성 값을 자식 구성 요소에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-155">Binding recognizes component parameters, where `@bind-{property}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="91564-156">자식에서 부모로의 바인딩은 연결 된 bind 섹션을 [사용 하는 자식-부모 바인딩에](#child-to-parent-binding-with-chained-bind) 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-156">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="91564-157">다음 자식 구성 요소 (`ChildComponent`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-157">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="91564-158">`EventCallback<T>`은 <xref:blazor/event-handling#eventcallback>에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-158">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="91564-159">다음 부모 구성 요소는을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-159">The following parent component uses:</span></span>

* <span data-ttu-id="91564-160">부모의 `ParentYear` 매개 변수를 `ChildComponent` 하 고 자식 구성 요소의 `Year` 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-160">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="91564-161">`onclick` 이벤트는 `ChangeTheYear` 메서드를 트리거하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-161">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="91564-162">자세한 내용은 <xref:blazor/event-handling>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91564-162">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="91564-163">`ParentComponent` 로드 하면 다음과 같은 태그가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-163">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="91564-164">`ParentComponent`의 단추를 선택 하 여 `ParentYear` 속성 값이 변경 되 면 `ChildComponent`의 `Year` 속성이 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-164">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="91564-165">`Year`의 새 값은 `ParentComponent`을 수행 하는 경우 UI에서 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-165">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="91564-166">`Year` 매개 변수는 `Year` 매개 변수 형식과 일치 하는 도우미 `YearChanged` 이벤트를 포함 하기 때문에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-166">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="91564-167">규칙에 따라 `<ChildComponent @bind-Year="ParentYear" />`는 기본적으로 작성 하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-167">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="91564-168">일반적으로 속성은 `@bind-property:event` 특성을 사용 하 여 해당 이벤트 처리기에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-168">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="91564-169">예를 들어 `MyProp` 속성은 다음 두 가지 특성을 사용 하 여 `MyEventHandler`에 바인딩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-169">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="91564-170">연결 된 바인딩을 사용 하는 자식-부모 바인딩</span><span class="sxs-lookup"><span data-stu-id="91564-170">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="91564-171">일반적인 시나리오는 데이터 바인딩된 매개 변수를 구성 요소 출력의 페이지 요소에 연결 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="91564-171">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="91564-172">이 시나리오를 여러 수준의 바인딩이 동시에 발생 하기 때문에 연결 된 *바인딩* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-172">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="91564-173">페이지의 요소에 `@bind` 구문을 사용 하 여 연결 된 바인딩을 구현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-173">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="91564-174">이벤트 처리기 및 값은 별도로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-174">The event handler and value must be specified separately.</span></span> <span data-ttu-id="91564-175">그러나 부모 구성 요소는 구성 요소의 매개 변수와 함께 `@bind` 구문을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91564-175">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="91564-176">다음 `PasswordField` 구성 요소 (*Passwordfield. razor*):</span><span class="sxs-lookup"><span data-stu-id="91564-176">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="91564-177">`<input>` 요소의 값을 `Password` 속성으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-177">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="91564-178">[Eventcallback](xref:blazor/event-handling#eventcallback)을 사용 하 여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-178">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="91564-179">`ToggleShowPassword` 메서드를 트리거하는 데 사용 되는 `onclick` 이벤트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-179">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="91564-180">자세한 내용은 <xref:blazor/event-handling>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="91564-180">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="91564-181">`PasswordField` 구성 요소는 다른 구성 요소에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="91564-181">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="91564-182">위의 예에서 암호에 대 한 검사 또는 트랩 오류를 수행 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-182">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="91564-183">`Password`에 대 한 지원 필드를 만듭니다 (다음 예제 코드에서`_password`).</span><span class="sxs-lookup"><span data-stu-id="91564-183">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="91564-184">`Password` setter에서 확인 또는 트랩 오류를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-184">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="91564-185">다음 예에서는 암호 값에 공백을 사용 하는 경우 사용자에 게 즉각적인 피드백을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="91564-185">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

### <a name="radio-buttons"></a><span data-ttu-id="91564-186">라디오 단추</span><span class="sxs-lookup"><span data-stu-id="91564-186">Radio buttons</span></span>

<span data-ttu-id="91564-187">폼의 라디오 단추에 바인딩하는 방법에 대 한 자세한 내용은 <xref:blazor/forms-validation#work-with-radio-buttons>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="91564-187">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
