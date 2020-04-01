---
title: ASP.NET Core Blazor 양식 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218962"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="1c3d2-103">ASP.NET Core Blazor 양식 및 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="1c3d2-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="1c3d2-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1c3d2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1c3d2-105">양식 및 유효성 검사는 [데이터 주석](xref:mvc/models/validation)을 사용하여 Blazor에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="1c3d2-106">다음 `ExampleModel` 형식은 데이터 주석을 사용하여 유효성 검사 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="1c3d2-107">양식은 `EditForm` 구성 요소를 사용하여 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="1c3d2-108">다음 양식은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="1c3d2-109">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="1c3d2-109">In the preceding example:</span></span>

* <span data-ttu-id="1c3d2-110">양식은 `ExampleModel` 형식에서 정의된 유효성 검사를 사용하여 `name` 필드에서 사용자 입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="1c3d2-111">모델은 구성 요소의 `@code` 블록에 생성되고 프라이빗 필드(`_exampleModel`)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="1c3d2-112">필드는 `<EditForm>` 요소의 `Model` 특성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="1c3d2-113">`InputText` 구성 요소의 `@bind-Value`는 다음과 같이 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="1c3d2-114">모델 속성(`_exampleModel.Name`)을 `InputText` 구성 요소의 `Value` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="1c3d2-115">변경 이벤트 대리자를 `InputText` 구성 요소의 `ValueChanged` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="1c3d2-116">`DataAnnotationsValidator` 구성 요소는 데이터 주석을 사용하여 유효성 검사 지원을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="1c3d2-117">`ValidationSummary` 구성 요소는 유효성 검사 메시지를 요약합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="1c3d2-118">양식이 성공적으로 제출되면(유효성 검사 통과) `HandleValidSubmit`가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="1c3d2-119">기본 제공 입력 구성 요소 집합을 사용하여 사용자 입력을 받고 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="1c3d2-120">입력을 변경할 때와 양식을 제출할 때 입력의 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="1c3d2-121">사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="1c3d2-122">입력 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1c3d2-122">Input component</span></span> | <span data-ttu-id="1c3d2-123">렌더링 형식&hellip;</span><span class="sxs-lookup"><span data-stu-id="1c3d2-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="1c3d2-124">`EditForm`을 비롯한 모든 입력 구성 요소는 임의 특성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="1c3d2-125">구성 요소 매개 변수와 일치하지 않는 특성은 렌더링된 HTML 요소에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="1c3d2-126">입력 구성 요소는 편집 시 유효성 검사와 필드 상태에 따른 CSS 클래스 변경의 기본 동작을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="1c3d2-127">일부 구성 요소는 유용한 구문 분석 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="1c3d2-128">예를 들어 `InputDate` 및 `InputNumber`는 구문 분석할 수 없는 값을 유효성 검사 오류로 등록하여 정상적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="1c3d2-129">Null 값을 허용할 수 있는 형식은 대상 필드의 Null 허용 여부도 지원합니다(예: `int?`).</span><span class="sxs-lookup"><span data-stu-id="1c3d2-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="1c3d2-130">다음 `Starship` 형식은 이전 `ExampleModel`보다 큰 속성 및 데이터 주석 집합을 사용하여 유효성 검사 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="1c3d2-131">위의 예제에서 `Description`은 데이터 주석이 없으므로 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="1c3d2-132">다음 양식은 `Starship` 모델에서 정의된 유효성 검사를 사용하여 사용자 입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="1c3d2-133">`EditForm`은 수정된 필드와 현재 유효성 검사 메시지를 포함하여 편집 프로세스에 대한 메타데이터를 추적하는 [계단식 값](xref:blazor/components#cascading-values-and-parameters)으로 `EditContext`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="1c3d2-134">`EditForm`은 유효한 제출과 잘못된 제출에 대한 편리한 이벤트(`OnValidSubmit`, `OnInvalidSubmit`)도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="1c3d2-135">또는 `OnSubmit`를 사용하여 유효성 검사를 트리거하고 사용자 지정 유효성 검사 코드로 필드 값을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="1c3d2-136">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="1c3d2-136">In the following example:</span></span>

* <span data-ttu-id="1c3d2-137">**Submit** 단추를 선택하면 `HandleSubmit` 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="1c3d2-138">양식의 `EditContext`를 사용하여 양식의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="1c3d2-139">서버에서 웹 API 엔드포인트를 호출하는 `ServerValidate` 메서드에 `EditContext`를 전달하여 양식의 유효성을 추가로 검사합니다(‘표시되지 않음’). </span><span class="sxs-lookup"><span data-stu-id="1c3d2-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="1c3d2-140">`isValid`를 확인하여 클라이언트 쪽 및 서버 쪽 유효성 검사 결과에 따라 추가 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="1c3d2-141">입력 이벤트를 기반으로 하는 InputText</span><span class="sxs-lookup"><span data-stu-id="1c3d2-141">InputText based on the input event</span></span>

<span data-ttu-id="1c3d2-142">`InputText` 구성 요소를 사용하여 `change` 이벤트 대신 `input` 이벤트를 사용하는 사용자 지정 구성 요소를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="1c3d2-143">다음 태그를 사용하여 구성 요소를 만든 다음, `InputText` 같이 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="1c3d2-144">라디오 단추 작업</span><span class="sxs-lookup"><span data-stu-id="1c3d2-144">Work with radio buttons</span></span>

<span data-ttu-id="1c3d2-145">양식에서 라디오 단추를 사용하는 경우, 라디오 단추는 그룹으로 평가되기 때문에 데이터 바인딩이 다른 요소와 다르게 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="1c3d2-146">각 라디오 단추의 값은 고정되어 있지만 라디오 단추 그룹의 값은 선택한 라디오 단추의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="1c3d2-147">아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-147">The following example shows how to:</span></span>

* <span data-ttu-id="1c3d2-148">라디오 단추 그룹의 데이터 바인딩을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="1c3d2-149">사용자 지정 `InputRadio` 구성 요소를 사용하여 유효성 검사를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="1c3d2-150">다음 `EditForm`은 위의 `InputRadio` 구성 요소를 사용하여 사용자의 평가를 가져오고 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="1c3d2-151">유효성 검사 지원</span><span class="sxs-lookup"><span data-stu-id="1c3d2-151">Validation support</span></span>

<span data-ttu-id="1c3d2-152">`DataAnnotationsValidator` 구성 요소는 데이터 주석을 사용하여 유효성 검사 지원을 계단식 `EditContext`에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="1c3d2-153">데이터 주석을 통해 유효성 검사 지원을 사용하도록 설정하려면 이 명시적 제스처가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="1c3d2-154">데이터 주석이 아닌 다른 유효성 검사 시스템을 사용하려면 `DataAnnotationsValidator`를 사용자 지정 구현으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="1c3d2-155">참조 소스 [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)에서 ASP.NET Core 구현을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="1c3d2-156">는 다음 두 가지 유형의 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-156"> performs two types of validation:</span></span>

* <span data-ttu-id="1c3d2-157">‘필드 유효성 검사’는 사용자가 Tab 키를 눌러 필드를 벗어날 때 수행됩니다. </span><span class="sxs-lookup"><span data-stu-id="1c3d2-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="1c3d2-158">필드 유효성을 검사하는 동안 `DataAnnotationsValidator` 구성 요소는 보고된 모든 유효성 검사 결과를 필드에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="1c3d2-159">‘모델 유효성 검사’는 사용자가 양식을 제출할 때 수행됩니다. </span><span class="sxs-lookup"><span data-stu-id="1c3d2-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="1c3d2-160">모델 유효성을 검사하는 동안 `DataAnnotationsValidator` 구성 요소는 유효성 검사 결과에 보고된 멤버 이름을 기준으로 필드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="1c3d2-161">개별 멤버와 연결되지 않은 유효성 검사 결과는 필드가 아니라 모델과 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="1c3d2-162">유효성 검사 요약 및 유효성 검사 메시지 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1c3d2-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="1c3d2-163">`ValidationSummary` 구성 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 유사하게, 모든 유효성 검사 메시지를 요약합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="1c3d2-164">`Model` 매개 변수를 사용하여 특정 모델의 유효성 검사 메시지를 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="1c3d2-165">`ValidationMessage` 구성 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 유사하게, 특정 필드의 유효성 검사 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="1c3d2-166">모델 속성 이름을 지정하는 람다 식과 `For` 특성을 사용하여 유효성을 검사할 필드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="1c3d2-167">`ValidationMessage` 및 `ValidationSummary` 구성 요소는 임의 특성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="1c3d2-168">구성 요소 매개 변수와 일치하지 않는 특성은 생성된 `<div>` 또는 `<ul>` 요소에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="1c3d2-169">사용자 지정 유효성 검사 특성</span><span class="sxs-lookup"><span data-stu-id="1c3d2-169">Custom validation attributes</span></span>

<span data-ttu-id="1c3d2-170">[사용자 지정 유효성 검사 특성](xref:mvc/models/validation#custom-attributes)을 사용할 때 유효성 검사 결과가 필드와 올바르게 연결되도록 하려면 <xref:System.ComponentModel.DataAnnotations.ValidationResult>를 만들 때 유효성 검사 컨텍스트의 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="1c3d2-171"> 데이터 주석 유효성 검사 패키지</span><span class="sxs-lookup"><span data-stu-id="1c3d2-171"> data annotations validation package</span></span>

<span data-ttu-id="1c3d2-172">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)은 `DataAnnotationsValidator` 구성 요소를 사용하여 유효성 검사 환경 간격을 채우는 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="1c3d2-173">이 패키지는 현재 ‘실험적’입니다. </span><span class="sxs-lookup"><span data-stu-id="1c3d2-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="1c3d2-174">[CompareProperty] 특성</span><span class="sxs-lookup"><span data-stu-id="1c3d2-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="1c3d2-175"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>는 유효성 검사 결과를 특정 멤버에 연결하지 않으므로 `DataAnnotationsValidator` 구성 요소에서 제대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="1c3d2-176">이로 인해 제출 시 전체 모델의 유효성을 검사하는 경우와 필드 수준 유효성 검사 간에 동작이 일치하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="1c3d2-177">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ‘실험적’ 패키지는 해당 제한 사항을 해결하는 추가 유효성 검사 특성인 `ComparePropertyAttribute`를 도입합니다. </span><span class="sxs-lookup"><span data-stu-id="1c3d2-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="1c3d2-178">Blazor 앱에서 `[CompareProperty]`는 `[Compare]` 특성을 직접 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="1c3d2-179">중첩된 모델, 컬렉션 형식 및 복합 형식</span><span class="sxs-lookup"><span data-stu-id="1c3d2-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="1c3d2-180">는 기본 제공 `DataAnnotationsValidator`와 함께 데이터 주석을 사용하여 양식 입력의 유효성 검사를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="1c3d2-181">그러나 `DataAnnotationsValidator`는 컬렉션 형식 또는 복합 형식 속성이 아닌, 양식에 바인딩된 모델의 최상위 속성에 대해서만 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="1c3d2-182">컬렉션 형식 및 복합 형식 속성을 포함하여 바인딩된 모델의 전체 개체 그래프에 대해 유효성을 검사하려면 ‘실험적’ [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 패키지에서 제공하는 `ObjectGraphDataAnnotationsValidator`를 사용합니다. </span><span class="sxs-lookup"><span data-stu-id="1c3d2-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="1c3d2-183">`[ValidateComplexType]`을 사용하여 모델 속성에 주석을 답니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="1c3d2-184">다음 모델 클래스에서 `ShipDescription` 클래스는 모델이 양식에 바인딩된 경우 유효성을 검사할 추가 데이터 주석을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="1c3d2-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="1c3d2-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="1c3d2-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="1c3d2-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="1c3d2-187">양식 유효성 검사에 따라 제출 단추 사용</span><span class="sxs-lookup"><span data-stu-id="1c3d2-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="1c3d2-188">양식 유효성 검사에 따라 제출 단추를 사용하거나 사용하지 않도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="1c3d2-189">양식의 `EditContext`를 사용하여 구성 요소가 초기화될 때 모델을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="1c3d2-190">컨텍스트의 `OnFieldChanged` 콜백에서 양식의 유효성을 검사하여 제출 단추를 사용하거나 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="1c3d2-191">`Dispose` 메서드에서 이벤트 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="1c3d2-192">자세한 내용은 <xref:blazor/lifecycle#component-disposal-with-idisposable>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

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
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="1c3d2-193">위의 예제에서는 다음과 같은 경우 `_formInvalid`를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="1c3d2-194">양식에 유효한 기본값이 미리 로드된 경우</span><span class="sxs-lookup"><span data-stu-id="1c3d2-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="1c3d2-195">양식을 로드할 때 제출 단추를 사용할 수 있게 하려는 경우</span><span class="sxs-lookup"><span data-stu-id="1c3d2-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="1c3d2-196">이전 방법의 부작용으로, 사용자가 아무 필드나 하나를 조작하고 나면 `ValidationSummary` 구성 요소에 잘못된 필드가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="1c3d2-197">이 시나리오는 다음 방법 중 하나로 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="1c3d2-198">양식에서 `ValidationSummary` 구성 요소를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1c3d2-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="1c3d2-199">제출 단추를 선택할 때 `ValidationSummary` 구성 요소가 표시되도록 설정합니다(예: `HandleValidSubmit` 메서드에서).</span><span class="sxs-lookup"><span data-stu-id="1c3d2-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
