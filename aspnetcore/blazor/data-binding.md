---
title: ASP.NET Core Blazor 데이터 바인딩
author: guardrex
description: Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 기능에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320959"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Core Blazor 데이터 바인딩

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

Razor 구성 요소는 필드, 속성 또는 Razor 식 값을 사용하여 [`@bind`](xref:mvc/views/razor#bind)라는 HTML 요소를 통해 데이터 바인딩 기능을 제공합니다.

다음 예제에서는 `CurrentValue` 속성을 텍스트 상자의 값에 바인딩합니다.

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

텍스트 상자가 포커스를 잃으면 속성 값이 업데이트됩니다.

텍스트 상자는 속성 값 변경에 대한 대응이 아니라, 구성 요소가 렌더링되는 경우에만 UI에서 업데이트됩니다. 이벤트 처리기 코드를 실행하면 구성 요소가 자체적으로 렌더링되므로 속성 업데이트는 *일반적으로* 이벤트 처리기가 트리거되는 즉시 UI에 반영됩니다.

`@bind` 속성에 `CurrentValue`를 사용하는 것(`<input @bind="CurrentValue" />`)은 기본적으로 다음과 같습니다.

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

구성 요소가 렌더링되면 input 요소의 `value`를 `CurrentValue` 속성에서 가져옵니다. 사용자가 텍스트 상자에 입력을 하고 요소 포커스를 변경하면 `onchange` 이벤트가 발생하고 `CurrentValue` 속성이 변경된 값으로 설정됩니다. 실제로는 `@bind`에서 형식 변환이 수행되는 경우를 처리하므로 코드 생성은 더 복잡해집니다. 원칙적으로 `@bind`는 식의 현재 값을 `value` 특성과 연결하고 등록된 처리기를 사용하여 변경 내용을 처리합니다.

또한 `@bind:event` 매개 변수에 `event` 특성을 포함하여 다른 이벤트에 속성 또는 필드를 바인딩합니다. 다음 예에서는 `CurrentValue` 이벤트에서 `oninput` 속성을 바인딩합니다.

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

요소가 포커스를 잃을 때 발생하는 `onchange`와는 달리 텍스트 상자의 값이 변경될 때 `oninput`이 발생합니다.

`@bind-{ATTRIBUTE}` 이외의 요소 특성을 바인딩하려면 `@bind-{ATTRIBUTE}:event` 구문에 `value`를 사용합니다. 다음 예제에서는 `_paragraphStyle` 값이 변경될 때 단락 스타일이 업데이트됩니다.

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

특성 바인딩은 대/소문자를 구분합니다. 예를 들어, `@bind`는 유효하고 `@Bind`는 유효하지 않습니다.

## <a name="unparsable-values"></a>구문 분석할 수 없는 값

사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공하면 바인딩 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.

다음 시나리오를 고려하세요.

* `<input>` 요소는 초기 값 `int`을 사용하여 `123` 형식에 바인딩됩니다.

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* 사용자는 페이지에서 요소의 값을 `123.45`로 업데이트하고 요소 포커스를 변경합니다.

위의 시나리오에서 요소의 값은 `123`으로 되돌아갑니다. 값 `123.45`가 원래 값 `123`에 따라 거부되면 사용자는 해당 값이 수용되지 않았다는 것을 이해합니다.

기본적으로 바인딩은 요소의 `onchange` 이벤트(`@bind="{PROPERTY OR FIELD}"`)에 적용됩니다. `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`를 사용하여 다른 이벤트에 대한 바인딩을 트리거합니다. `oninput` 이벤트(`@bind:event="oninput"`)의 경우 구문 분석할 수 있는 값을 도입하는 키 입력 후에 되돌려집니다. `oninput` 이벤트의 대상을 `int` 바인딩 형식으로 지정하는 경우 사용자는 `.` 문자를 입력할 수 없게 됩니다. `.` 문자는 즉시 제거되므로 사용자는 정수만 허용된다는 즉각적인 피드백을 받습니다. 사용자가 구문 분석할 수 없는 `oninput` 값을 지울 수 있어야 하는 경우와 같이 `<input>` 이벤트의 값을 되돌리는 것이 적합하지 않은 경우가 있습니다. 대안은 다음과 같습니다.

* `oninput` 이벤트를 사용하지 마세요. 기본 `onchange` 이벤트를 사용합니다(`@bind="{PROPERTY OR FIELD}"`만 지정). 이 경우 요소가 포커스를 잃을 때까지 잘못된 값은 복귀되지 않습니다.
* `int?` 또는 `string`과 같은 nullable 형식에 바인딩하고 잘못된 항목을 처리하기 위한 사용자 지정 논리를 제공합니다.
* [ 또는 ](xref:blazor/forms-validation)와 같은 `InputNumber`양식 유효성 검사 구성 요소`InputDate`를 사용합니다. 양식 유효성 검사 구성 요소에는 잘못된 입력을 관리하기 위한 기본 제공 지원이 있습니다. 양식 유효성 검사 구성 요소:
  * 사용자가 연결된 `EditContext`에서 잘못된 입력을 제공하고 유효성 검사 오류를 수신할 수 있도록 허용합니다.
  * 사용자가 추가 Webform 데이터를 입력하는 것을 방해하지 않고 UI에서 유효성 검사 오류를 표시합니다.

## <a name="format-strings"></a>형식 문자열

데이터 바인딩은 <xref:System.DateTime>[`@bind:format`를 사용하여 ](xref:mvc/views/razor#bind) 형식 문자열에 작동합니다. 통화 또는 숫자 형식 등의 다른 형식 식은 현재 사용할 수 없습니다.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

위의 코드에서 `<input>` 요소의 필드 형식(`type`)은 기본적으로 `text`입니다. `@bind:format`은 다음 .NET 형식을 바인딩하는 데 지원됩니다.

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 특성은 `value` 요소의 `<input>`에 적용할 날짜 형식을 지정합니다. 이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석하는 데도 사용됩니다.

`date`에서는 기본적으로 날짜 형식을 지정할 수 있도록 지원하므로 Blazor 필드의 형식을 지정하는 것은 권장되지 않습니다. 권장 사항에도 불구하고 `yyyy-MM-dd` 필드의 형식이 제공된 경우 바인딩이 올바르게 작동하려면 `date` 날짜 형식만 사용합니다.

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>구성 요소 매개 변수를 사용한 부모-자식 바인딩

바인딩은 구성 요소 매개 변수를 인식합니다. 여기서 `@bind-{PROPERTY}`는 부모 구성 요소의 속성 값을 자식 구성 요소에 바인딩할 수 있습니다. 자식에서 부모로의 바인딩은 [체인 바인딩을 사용한 자식-부모 바인딩](#child-to-parent-binding-with-chained-bind)에 설명되어 있습니다.

다음 자식 구성 요소(`ChildComponent`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.

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

`EventCallback<T>`는 <xref:blazor/event-handling#eventcallback>에 설명됩니다.

다음 부모 구성 요소는

* `ChildComponent`를 사용하고 부모의 `ParentYear` 매개 변수를 자식 구성 요소의 `Year` 매개 변수에 바인딩합니다.
* `onclick` 이벤트는 `ChangeTheYear` 메서드를 트리거하는 데 사용됩니다. 자세한 내용은 <xref:blazor/event-handling>을 참조하세요.

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

`ParentComponent`를 로드하면 다음 태그가 생성됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear`의 단추를 선택 하 여 `ParentComponent` 속성 값이 변경 되 면 `Year`의 `ChildComponent` 속성이 업데이트 됩니다. `Year`의 새 값은 `ParentComponent`가 다시 렌더링될 때 UI에서 렌더링됩니다.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` 매개 변수는 `YearChanged` 매개 변수 형식과 일치하는 도우미 `Year` 이벤트를 포함하기 때문에 바인딩 가능합니다.

규칙에 따르면 `<ChildComponent @bind-Year="ParentYear" />`는 기본적으로 다음을 쓰는 것과 같습니다.

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

일반적으로 속성은 `@bind-{PROPRETY}:event` 특성을 포함하여 해당 이벤트 처리기에 바인딩할 수 있습니다. 예를 들어, `MyProp` 속성은 다음 두 가지 특성을 사용하여 `MyEventHandler`에 바인딩될 수 있습니다.

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>체인 바인딩을 사용한 자식-부모 바인딩

일반적인 시나리오는 데이터 바인딩 매개 변수를 구성 요소 출력의 페이지 요소에 체인하는 것입니다. 여러 수준의 바인딩이 동시에 발생하기 때문에 이 시나리오를 *체인 바인딩*이라고 합니다.

페이지의 요소에 `@bind` 구문을 사용하여 체인 바인딩을 구현할 수 없습니다. 이벤트 처리기 및 값은 별도로 지정해야 합니다. 그러나 부모 구성 요소는 구성 요소의 매개 변수에서 `@bind` 구문을 사용할 수 있습니다.

다음 `PasswordField` 구성 요소(*PasswordField. razor*):

* `<input>` 요소의 값을 `Password` 속성으로 설정합니다.
* `Password`EventCallback[을 사용하여 ](xref:blazor/event-handling#eventcallback) 속성의 변경 내용을 부모 구성 요소에 노출합니다.
* `onclick` 메서드를 트리거하는 데 `ToggleShowPassword` 이벤트를 사용합니다. 자세한 내용은 <xref:blazor/event-handling>을 참조하세요.

```razor
<h1>Child Component</h1>

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

`PasswordField` 구성 요소는 다른 구성 요소에서 사용됩니다.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

위의 예에서 암호에 대해 검사를 수행하거나 오류를 트랩하려면 다음을 수행합니다.

* `Password`에 대한 지원 필드를 만듭니다(다음 예제 코드의 `_password`).
* `Password` setter에서 검사를 수행하거나 오류를 트랩합니다.

다음 예에서는 암호 값에 공백을 사용하는 경우 사용자에게 즉각적인 피드백을 제공합니다.

```razor
<h1>Child Component</h1>

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

## <a name="radio-buttons"></a>라디오 단추

양식의 라디오 단추에 바인딩하는 방법에 대한 내용은 <xref:blazor/forms-validation#work-with-radio-buttons>를 참조하세요.
