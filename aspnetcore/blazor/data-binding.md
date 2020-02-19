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
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Core Blazor 데이터 바인딩

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

구성 요소와 DOM 요소 모두에 대 한 데이터 바인딩은 [`@bind`](xref:mvc/views/razor#bind) 특성을 사용 하 여 수행 됩니다. 다음 예제에서는 `CurrentValue` 속성을 텍스트 상자의 값에 바인딩합니다.

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

텍스트 상자가 포커스를 잃으면 속성의 값이 업데이트 됩니다.

텍스트 상자는 구성 요소가 렌더링 되는 경우에만 UI에서 업데이트 되며 속성의 값을 변경 하는 것에 대 한 응답으로는 업데이트 되지 않습니다. 이벤트 처리기 코드를 실행 한 후 구성 요소를 자체적으로 렌더링 하므로 속성 업데이트는 *일반적으로* 이벤트 처리기가 트리거되는 즉시 UI에 반영 됩니다.

`CurrentValue` 속성 (`<input @bind="CurrentValue" />`)과 함께 `@bind`를 사용 하는 것은 기본적으로 다음과 같습니다.

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

구성 요소가 렌더링 되 면 input 요소의 `value` `CurrentValue` 속성에서 제공 됩니다. 사용자가 텍스트 상자에를 입력 하 고 요소 포커스를 변경 하면 `onchange` 이벤트가 발생 하 고 `CurrentValue` 속성이 변경 된 값으로 설정 됩니다. 실제로는 `@bind`에서 형식 변환이 수행 되는 경우를 처리 하므로 코드 생성은 더 복잡 합니다. 원칙적으로 `@bind`은 식의 현재 값을 `value` 특성과 연결 하 고 등록 된 처리기를 사용 하 여 변경 내용을 처리 합니다.

`@bind` 구문을 사용 하 여 `onchange` 이벤트를 처리 하는 것 외에도 `event` 매개 변수 ([`@bind-value:event`](xref:mvc/views/razor#bind))를 사용 하 여 [`@bind-value`](xref:mvc/views/razor#bind) 특성을 지정 하 여 다른 이벤트를 사용 하 여 속성 또는 필드를 바인딩할 수 있습니다. 다음 예에서는 `oninput` 이벤트의 `CurrentValue` 속성을 바인딩합니다.

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

요소가 포커스를 잃을 때 발생 하는 `onchange`와는 달리 텍스트 상자의 값이 변경 될 때 발생 `oninput`.

이전 예제 바인딩의 `@bind-value`:

* 요소의 `value` 특성에 대 한 지정 된 식 (`CurrentValue`)입니다.
* `@bind-value:event`으로 지정 된 이벤트에 대 한 변경 이벤트 대리자입니다.

### <a name="unparsable-values"></a>구문 분석할 값

사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공 하면 bind 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.

다음과 같은 시나리오를 고려해 보세요.

* `<input>` 요소는 `123`초기 값을 사용 하 여 `int` 형식에 바인딩됩니다.

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* 사용자는 요소의 값을 업데이트 하 여 페이지에서 `123.45` 하 고 요소 포커스를 변경 합니다.

위의 시나리오에서 요소의 값은 `123`로 되돌아갑니다. `123`의 원래 값을 사용 하 여 `123.45` 값이 거부 되 면 사용자는 해당 값이 허용 되지 않는다는 것을 이해 합니다.

기본적으로 바인딩은 요소의 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)에 적용 됩니다. `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`를 사용 하 여 다른 이벤트를 설정 합니다. `oninput` 이벤트 (`@bind-value:event="oninput"`)의 경우 변경할지는 구문 분석할 값을 제공 하는 키 입력 후에 발생 합니다. `int`바인딩된 형식의 `oninput` 이벤트를 대상으로 지정 하는 경우 사용자는 `.` 문자를 입력할 수 없습니다. `.` 문자는 즉시 제거 되므로 사용자는 전체 숫자만 허용 되는 즉각적인 피드백을 받습니다. 사용자가 구문 분석할 수 없는 `<input>` 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합 하지 않은 시나리오가 있습니다. 대안은 다음과 같습니다.

* `oninput` 이벤트를 사용 하지 마세요. 요소가 포커스를 잃을 때까지 잘못 된 값이 되돌아가지 않는 기본 `onchange` 이벤트 (`@bind="{PROPERTY OR FIELD}"`)를 사용 합니다.
* `int?` 또는 `string`와 같은 nullable 형식에 바인딩하고 잘못 된 항목을 처리 하는 사용자 지정 논리를 제공 합니다.
* `InputNumber` 또는 `InputDate`와 같은 [폼 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용 합니다. 양식 유효성 검사 구성 요소에는 잘못 된 입력을 관리 하기 위한 기본 제공 지원이 있습니다. 양식 유효성 검사 구성 요소:
  * 사용자가 연결 된 `EditContext`에서 잘못 된 입력 및 수신 유효성 검사 오류를 제공할 수 있도록 허용 합니다.
  * 사용자가 추가 webform 데이터를 입력 하는 것을 방해 하지 않고 UI에서 유효성 검사 오류를 표시 합니다.

### <a name="format-strings"></a>서식 문자열

데이터 바인딩은 [`@bind:format`](xref:mvc/views/razor#bind)를 사용 하 여 <xref:System.DateTime> 형식 문자열과 함께 작동 합니다. 통화 또는 숫자 형식과 같은 다른 형식 식은 현재 사용할 수 없습니다.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

위의 코드에서 `<input>` 요소의 필드 형식 (`type`)은 기본적으로 `text`입니다. 다음 .NET 형식의 바인딩에 대해 `@bind:format` 지원 됩니다.

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정 합니다. 이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석 하는 데도 사용 됩니다.

Blazor에서 날짜 형식을 기본적으로 지원 하기 때문에 `date` 필드 형식에 대 한 형식을 지정 하는 것은 권장 되지 않습니다. 권장 사항에도 불구 하 고 `date` 필드 형식과 함께 형식이 제공 되는 경우 바인딩이 제대로 작동 하려면 `yyyy-MM-dd` 날짜 형식만 사용 합니다.

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a>구성 요소 매개 변수를 사용 하 여 부모-자식 바인딩

바인딩은 구성 요소 매개 변수를 인식 합니다. 여기서 `@bind-{property}`는 부모 구성 요소의 속성 값을 자식 구성 요소에 바인딩할 수 있습니다. 자식에서 부모로의 바인딩은 연결 된 bind 섹션을 [사용 하는 자식-부모 바인딩에](#child-to-parent-binding-with-chained-bind) 포함 됩니다.

다음 자식 구성 요소 (`ChildComponent`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.

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

`EventCallback<T>`은 <xref:blazor/event-handling#eventcallback>에 설명 되어 있습니다.

다음 부모 구성 요소는을 사용 합니다.

* 부모의 `ParentYear` 매개 변수를 `ChildComponent` 하 고 자식 구성 요소의 `Year` 매개 변수에 바인딩합니다.
* `onclick` 이벤트는 `ChangeTheYear` 메서드를 트리거하는 데 사용 됩니다. 자세한 내용은 <xref:blazor/event-handling>을 참조하세요.

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

`ParentComponent` 로드 하면 다음과 같은 태그가 생성 됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentComponent`의 단추를 선택 하 여 `ParentYear` 속성 값이 변경 되 면 `ChildComponent`의 `Year` 속성이 업데이트 됩니다. `Year`의 새 값은 `ParentComponent`을 수행 하는 경우 UI에서 렌더링 됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` 매개 변수는 `Year` 매개 변수 형식과 일치 하는 도우미 `YearChanged` 이벤트를 포함 하기 때문에 바인딩할 수 있습니다.

규칙에 따라 `<ChildComponent @bind-Year="ParentYear" />`는 기본적으로 작성 하는 것과 같습니다.

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

일반적으로 속성은 `@bind-property:event` 특성을 사용 하 여 해당 이벤트 처리기에 바인딩할 수 있습니다. 예를 들어 `MyProp` 속성은 다음 두 가지 특성을 사용 하 여 `MyEventHandler`에 바인딩될 수 있습니다.

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a>연결 된 바인딩을 사용 하는 자식-부모 바인딩

일반적인 시나리오는 데이터 바인딩된 매개 변수를 구성 요소 출력의 페이지 요소에 연결 하는 것입니다. 이 시나리오를 여러 수준의 바인딩이 동시에 발생 하기 때문에 연결 된 *바인딩* 이라고 합니다.

페이지의 요소에 `@bind` 구문을 사용 하 여 연결 된 바인딩을 구현할 수 없습니다. 이벤트 처리기 및 값은 별도로 지정 해야 합니다. 그러나 부모 구성 요소는 구성 요소의 매개 변수와 함께 `@bind` 구문을 사용할 수 있습니다.

다음 `PasswordField` 구성 요소 (*Passwordfield. razor*):

* `<input>` 요소의 값을 `Password` 속성으로 설정 합니다.
* [Eventcallback](xref:blazor/event-handling#eventcallback)을 사용 하 여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출 합니다.
* `ToggleShowPassword` 메서드를 트리거하는 데 사용 되는 `onclick` 이벤트를 사용 합니다. 자세한 내용은 <xref:blazor/event-handling>을 참조하세요.

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

`PasswordField` 구성 요소는 다른 구성 요소에서 사용 됩니다.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

위의 예에서 암호에 대 한 검사 또는 트랩 오류를 수행 하려면 다음을 수행 합니다.

* `Password`에 대 한 지원 필드를 만듭니다 (다음 예제 코드에서`_password`).
* `Password` setter에서 확인 또는 트랩 오류를 수행 합니다.

다음 예에서는 암호 값에 공백을 사용 하는 경우 사용자에 게 즉각적인 피드백을 제공 합니다.

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

### <a name="radio-buttons"></a>라디오 단추

폼의 라디오 단추에 바인딩하는 방법에 대 한 자세한 내용은 <xref:blazor/forms-validation#work-with-radio-buttons>를 참조 하세요.
