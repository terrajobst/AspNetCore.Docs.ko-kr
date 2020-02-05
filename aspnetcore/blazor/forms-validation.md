---
title: ASP.NET Core Blazor 폼 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 2758bcbbc76c8a59716fe224dd2deb4ca8c06929
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726888"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>ASP.NET Core [!OP.NO-LOC(Blazor)] 폼 및 유효성 검사

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

폼 및 유효성 검사는 [데이터 주석을](xref:mvc/models/validation)사용 하 여 [!OP.NO-LOC(Blazor)]에서 지원 됩니다.

다음 `ExampleModel` 형식은 데이터 주석을 사용 하 여 유효성 검사 논리를 정의 합니다.

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

폼은 `EditForm` 구성 요소를 사용 하 여 정의 됩니다. 다음 폼은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

앞의 예제에서:

* 양식은 `ExampleModel` 유형에 정의 된 유효성 검사를 사용 하 여 `name` 필드에서 사용자 입력의 유효성을 검사 합니다. 모델은 구성 요소의 `@code` 블록에 만들어지고 전용 필드 (`_exampleModel`)에 저장 됩니다. 필드는 `<EditForm>` 요소의 `Model` 특성에 할당 됩니다.
* `InputText` 구성 요소의 `@bind-Value` 바인딩합니다.
  * `InputText` 구성 요소의 `Value` 속성에 대 한 모델 속성 (`_exampleModel.Name`)입니다.
  * `InputText` 구성 요소의 `ValueChanged` 속성에 대 한 변경 이벤트 대리자입니다.
* `DataAnnotationsValidator` 구성 요소는 데이터 주석을 사용 하 여 유효성 검사 지원을 연결 합니다.
* `ValidationSummary` 구성 요소는 유효성 검사 메시지를 요약 합니다.
* 양식이 성공적으로 제출 되 면 (유효성 검사 통과) `HandleValidSubmit` 트리거됩니다.

사용자 입력을 수신 하 고 유효성을 검사 하는 데 사용할 수 있는 기본 제공 입력 구성 요소 집합입니다. 입력이 변경 되 고 양식이 제출 될 때 유효성 검사가 수행 됩니다. 사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.

| 입력 구성 요소 | &hellip;으로 렌더링 됨       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

`EditForm`를 비롯 한 모든 입력 구성 요소는 임의 특성을 지원 합니다. 구성 요소 매개 변수와 일치 하지 않는 특성은 렌더링 된 HTML 요소에 추가 됩니다.

입력 구성 요소는 편집 시 유효성을 검사 하 고 CSS 클래스를 변경 하 여 필드 상태를 반영 하는 기본 동작을 제공 합니다. 일부 구성 요소는 유용한 구문 분석 논리를 포함 합니다. 예를 들어 `InputDate` 및 `InputNumber`는 유효성 검사 오류로 등록 하 여 구문 분석할 수 값을 적절 하 게 처리 합니다. Null 값을 사용할 수 있는 형식은 대상 필드의 null 허용 여부도 지원 합니다 (예: `int?`).

다음 `Starship` 형식은 이전 `ExampleModel`보다 큰 속성 및 데이터 주석의 집합을 사용 하 여 유효성 검사 논리를 정의 합니다.

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

앞의 예제에서 데이터 주석이 없으므로 `Description`은 선택 사항입니다.

다음 폼은 `Starship` 모델에 정의 된 유효성 검사를 사용 하 여 사용자 입력의 유효성을 검사 합니다.

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

`EditForm`은 수정 된 필드 및 현재 유효성 검사 메시지를 포함 하 여 편집 프로세스에 대 한 메타 데이터를 추적 하는 [연계 값](xref:blazor/components#cascading-values-and-parameters) 으로 `EditContext`를 만듭니다. `EditForm`는 유효한 전송 (`OnValidSubmit`, `OnInvalidSubmit`)에도 편리한 이벤트를 제공 합니다. 또는 `OnSubmit`를 사용 하 여 유효성 검사를 트리거하고 사용자 지정 유효성 검사 코드를 사용 하 여 필드 값을 확인 합니다.

다음 예제에서는

* **전송** 단추를 선택 하면 `HandleSubmit` 메서드가 실행 됩니다.
* 양식의 `EditContext`를 사용 하 여 폼의 유효성을 검사 합니다.
* 서버에서 web API 끝점을 호출 하는 `ServerValidate` 메서드에 `EditContext`를 전달 하 여 폼의 유효성을 검사 합니다 (*표시 되지 않음*).
* `isValid`를 확인 하 여 클라이언트 및 서버 쪽 유효성 검사의 결과에 따라 추가 코드를 실행 합니다.

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>입력 이벤트를 기반으로 하는 InputText

`InputText` 구성 요소를 사용 하 여 `change` 이벤트 대신 `input` 이벤트를 사용 하는 사용자 지정 구성 요소를 만듭니다.

다음 태그를 사용 하 여 구성 요소를 만들고 `InputText` 사용 되는 것 처럼 구성 요소를 사용 합니다.

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>라디오 단추 작업

폼에서 라디오 단추를 사용할 때 라디오 단추가 그룹으로 평가 되기 때문에 데이터 바인딩은 다른 요소와 다르게 처리 됩니다. 각 라디오 단추의 값은 고정 되어 있지만 라디오 단추 그룹의 값은 선택 된 라디오 단추의 값입니다. 아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

* 라디오 단추 그룹에 대 한 데이터 바인딩을 처리 합니다.
* 사용자 지정 `InputRadio` 구성 요소를 사용 하 여 유효성 검사를 지원 합니다.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

다음 `EditForm`에서는 위의 `InputRadio` 구성 요소를 사용 하 여 사용자의 등급을 가져오고 유효성을 검사 합니다.

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>유효성 검사 지원

`DataAnnotationsValidator` 구성 요소는 데이터 주석을 사용 하 여 종속 된 `EditContext`에 대 한 유효성 검사 지원을 연결 합니다. 데이터 주석을 사용 하 여 유효성 검사 지원을 활성화 하려면이 명시적인 제스처가 필요 합니다. 데이터 주석과 다른 유효성 검사 시스템을 사용 하려면 `DataAnnotationsValidator`를 사용자 지정 구현으로 바꿉니다. ASP.NET Core 구현은 참조 소스: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)에서 검사할 수 있습니다.

Blazor는 두 가지 유형의 유효성 검사를 수행 합니다.

* 필드 *유효성 검사* 는 사용자가 필드 밖으로 탭 할 때 수행 됩니다. 필드의 유효성을 검사 하는 동안 `DataAnnotationsValidator` 구성 요소는 보고 된 모든 유효성 검사 결과를 필드와 연결 합니다.
* 사용자가 폼을 제출 하면 *모델 유효성 검사가* 수행 됩니다. 모델 유효성 검사 중에 `DataAnnotationsValidator` 구성 요소는 유효성 검사 결과가 보고 하는 멤버 이름을 기준으로 필드를 결정 합니다. 개별 멤버와 연결 되지 않은 유효성 검사 결과는 필드가 아니라 모델과 연결 됩니다.

### <a name="validation-summary-and-validation-message-components"></a>유효성 검사 요약 및 유효성 검사 메시지 구성 요소

`ValidationSummary` 구성 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 비슷한 모든 유효성 검사 메시지를 요약 합니다.

```razor
<ValidationSummary />
```

`Model` 매개 변수를 사용 하 여 특정 모델에 대 한 유효성 검사 메시지를 출력 합니다.
  
```razor
<ValidationSummary Model="@_starship" />
```

`ValidationMessage` 구성 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 비슷한 특정 필드에 대 한 유효성 검사 메시지를 표시 합니다. `For` 특성과 모델 속성의 이름을 지정 하는 람다 식을 사용 하 여 유효성을 검사 하기 위한 필드를 지정 합니다.

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

`ValidationMessage` 및 `ValidationSummary` 구성 요소는 임의 특성을 지원 합니다. 구성 요소 매개 변수와 일치 하지 않는 특성은 생성 된 `<div>` 또는 `<ul>` 요소에 추가 됩니다.

### <a name="custom-validation-attributes"></a>사용자 지정 유효성 검사 특성

[사용자 지정 유효성 검사 특성](xref:mvc/models/validation#custom-attributes)을 사용 하는 경우 유효성 검사 결과가 필드와 올바르게 연결 되도록 하려면 <xref:System.ComponentModel.DataAnnotations.ValidationResult>를 만들 때 유효성 검사 컨텍스트의 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>를 전달 합니다.

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>데이터 주석 유효성 검사 패키지 Blazor

[AspNetCoreBlazor입니다. DataAnnotations. 유효성 검사](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) 는 `DataAnnotationsValidator` 구성 요소를 사용 하 여 유효성 검사 환경 간격을 채우는 패키지입니다. 패키지가 현재 *실험적*입니다.

### <a name="compareproperty-attribute"></a>[CompareProperty] 특성

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>는 유효성 검사 결과를 특정 멤버와 연결 하지 않기 때문에 `DataAnnotationsValidator` 구성 요소에서 제대로 작동 하지 않습니다. 이 경우 필드 수준 유효성 검사와 전송에서 전체 모델의 유효성을 검사할 때 일관 되지 않은 동작이 발생할 수 있습니다. [AspNetCoreBlazor입니다. DataAnnotations. 유효성 검사](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *실험적* 패키지는 이러한 제한을 해결 하는 추가 유효성 검사 특성 `ComparePropertyAttribute`을 제공 합니다. Blazor 앱에서 `[CompareProperty]`는 `[Compare]` 특성을 직접 대체 합니다.

### <a name="nested-models-collection-types-and-complex-types"></a>중첩 된 모델, 컬렉션 형식 및 복합 형식

Blazor은 기본 제공 `DataAnnotationsValidator`에서 데이터 주석을 사용 하 여 양식 입력의 유효성을 검사 하는 기능을 지원 합니다. 그러나 `DataAnnotationsValidator`는 컬렉션 또는 복합 형식 속성이 아닌 폼에 바인딩된 모델의 최상위 속성에 대해서만 유효성을 검사 합니다.

컬렉션 및 복합 형식 속성을 포함 하 여 바인딩된 모델의 전체 개체 그래프의 유효성을 검사 하려면BlazorAspNetCore에서 *제공 하는* `ObjectGraphDataAnnotationsValidator`을 사용 합니다. [ DataAnnotations. 유효성 검사](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) 패키지:

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

`[ValidateComplexType]`를 사용 하 여 모델 속성에 주석을 추가 합니다. 다음 모델 클래스에서 `ShipDescription` 클래스에는 모델이 폼에 바인딩될 때 유효성을 검사 하기 위한 추가 데이터 주석이 포함 되어 있습니다.

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>양식 유효성 검사에 따라 제출 단추를 사용 하도록 설정

양식 유효성 검사에 따라 제출 단추를 사용 하거나 사용 하지 않도록 설정 하려면:

* 구성 요소가 초기화 될 때 폼의 `EditContext`를 사용 하 여 모델을 할당 합니다.
* 컨텍스트의 `OnFieldChanged` 콜백에서 폼의 유효성을 검사 하 여 제출 단추를 사용 하거나 사용 하지 않도록 설정 합니다.

```razor
<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);

        _editContext.OnFieldChanged += (_, __) =>
        {
            _formInvalid = !_editContext.Validate();
            StateHasChanged();
        };
    }
}
```

앞의 예제에서 다음과 같은 경우 `_formInvalid`를 `false`로 설정 합니다.

* 올바른 기본값이 포함 된 양식이 미리 로드 됩니다.
* 양식이 로드 될 때 제출 단추를 사용 하도록 설정 하려고 합니다.

이전 방법의 부작용은 사용자가 한 필드와 상호 작용 하 고 나면 `ValidationSummary` 구성 요소가 잘못 된 필드로 채워지는 것입니다. 이 시나리오는 다음 중 한 가지 방법으로 해결할 수 있습니다.

* 폼에 `ValidationSummary` 구성 요소를 사용 하지 마십시오.
* 전송 단추가 선택 된 경우 (예: `HandleValidSubmit` 메서드에서) `ValidationSummary` 구성 요소를 표시 하도록 설정 합니다.

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
