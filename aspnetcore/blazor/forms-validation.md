---
title: ASP.NET Core Blazor 폼 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
uid: blazor/forms-validation
ms.openlocfilehash: f1df213b16bb7ecd6a771700291d834776dee475
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317176"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="cfd2c-103">ASP.NET Core Blazor 폼 및 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="cfd2c-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="cfd2c-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cfd2c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cfd2c-105">폼 및 유효성 검사는 [데이터 주석을](xref:mvc/models/validation)사용 하 여 Blazor에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="cfd2c-106">다음 `ExampleModel` 형식은 데이터 주석을 사용 하 여 유효성 검사 논리를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="cfd2c-107">폼은 `EditForm` 구성 요소를 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="cfd2c-108">다음 폼은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="cfd2c-109">양식은 `ExampleModel` 유형에 정의 된 유효성 검사를 사용 하 여 `name` 필드에서 사용자 입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="cfd2c-110">모델은 구성 요소의 `@code` 블록에 만들어지고 전용 필드 (`exampleModel`)에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="cfd2c-111">필드는 `<EditForm>` 요소의 `Model` 특성에 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="cfd2c-112">`DataAnnotationsValidator` 구성 요소는 데이터 주석을 사용 하 여 유효성 검사 지원을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="cfd2c-113">`ValidationSummary` 구성 요소는 유효성 검사 메시지를 요약 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="cfd2c-114">양식이 성공적으로 제출 되 면 (유효성 검사 통과) `HandleValidSubmit` 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="cfd2c-115">사용자 입력을 수신 하 고 유효성을 검사 하는 데 사용할 수 있는 기본 제공 입력 구성 요소 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="cfd2c-116">입력이 변경 되 고 양식이 제출 될 때 유효성 검사가 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="cfd2c-117">사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="cfd2c-118">입력 구성 요소</span><span class="sxs-lookup"><span data-stu-id="cfd2c-118">Input component</span></span> | <span data-ttu-id="cfd2c-119">&hellip;으로 렌더링 됨</span><span class="sxs-lookup"><span data-stu-id="cfd2c-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="cfd2c-120">`EditForm`를 비롯 한 모든 입력 구성 요소는 임의 특성을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="cfd2c-121">구성 요소 매개 변수와 일치 하지 않는 특성은 렌더링 된 HTML 요소에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="cfd2c-122">입력 구성 요소는 편집 시 유효성을 검사 하 고 CSS 클래스를 변경 하 여 필드 상태를 반영 하는 기본 동작을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="cfd2c-123">일부 구성 요소는 유용한 구문 분석 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="cfd2c-124">예를 들어 `InputDate` 및 `InputNumber`는 유효성 검사 오류로 등록 하 여 구문 분석할 수 값을 적절 하 게 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="cfd2c-125">Null 값을 사용할 수 있는 형식은 대상 필드의 null 허용 여부도 지원 합니다 (예: `int?`).</span><span class="sxs-lookup"><span data-stu-id="cfd2c-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="cfd2c-126">다음 `Starship` 형식은 이전 `ExampleModel`보다 큰 속성 및 데이터 주석의 집합을 사용 하 여 유효성 검사 논리를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="cfd2c-127">앞의 예제에서 데이터 주석이 없으므로 `Description`은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="cfd2c-128">다음 폼은 `Starship` 모델에 정의 된 유효성 검사를 사용 하 여 사용자 입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="cfd2c-129">`EditForm`은 수정 된 필드 및 현재 유효성 검사 메시지를 포함 하 여 편집 프로세스에 대 한 메타 데이터를 추적 하는 [연계 값](xref:blazor/components#cascading-values-and-parameters) 으로 `EditContext`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="cfd2c-130">`EditForm`는 유효한 전송 (`OnValidSubmit`, `OnInvalidSubmit`)에도 편리한 이벤트를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="cfd2c-131">또는 `OnSubmit`를 사용 하 여 유효성 검사를 트리거하고 사용자 지정 유효성 검사 코드를 사용 하 여 필드 값을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="cfd2c-132">입력 이벤트를 기반으로 하는 InputText</span><span class="sxs-lookup"><span data-stu-id="cfd2c-132">InputText based on the input event</span></span>

<span data-ttu-id="cfd2c-133">`InputText` 구성 요소를 사용 하 여 `change` 이벤트 대신 `input` 이벤트를 사용 하는 사용자 지정 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="cfd2c-134">다음 태그를 사용 하 여 구성 요소를 만들고 `InputText` 사용 되는 것 처럼 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="cfd2c-135">유효성 검사 지원</span><span class="sxs-lookup"><span data-stu-id="cfd2c-135">Validation support</span></span>

<span data-ttu-id="cfd2c-136">`DataAnnotationsValidator` 구성 요소는 데이터 주석을 사용 하 여 종속 된 `EditContext`에 대 한 유효성 검사 지원을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="cfd2c-137">데이터 주석을 사용 하 여 유효성 검사 지원을 활성화 하려면이 명시적인 제스처가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="cfd2c-138">데이터 주석과 다른 유효성 검사 시스템을 사용 하려면 `DataAnnotationsValidator`를 사용자 지정 구현으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="cfd2c-139">ASP.NET Core 구현은 참조 소스: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)에서 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="cfd2c-140">는 두 가지 유형의 유효성 검사를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-140"> performs two types of validation:</span></span>

* <span data-ttu-id="cfd2c-141">필드 *유효성 검사* 는 사용자가 필드 밖으로 탭 할 때 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-141">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="cfd2c-142">필드의 유효성을 검사 하는 동안 `DataAnnotationsValidator` 구성 요소는 보고 된 모든 유효성 검사 결과를 필드와 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-142">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="cfd2c-143">사용자가 폼을 제출 하면 *모델 유효성 검사가* 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-143">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="cfd2c-144">모델 유효성 검사 중에 `DataAnnotationsValidator` 구성 요소는 유효성 검사 결과가 보고 하는 멤버 이름을 기준으로 필드를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-144">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="cfd2c-145">개별 멤버와 연결 되지 않은 유효성 검사 결과는 필드가 아니라 모델과 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-145">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="cfd2c-146">유효성 검사 요약 및 유효성 검사 메시지 구성 요소</span><span class="sxs-lookup"><span data-stu-id="cfd2c-146">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="cfd2c-147">`ValidationSummary` 구성 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 비슷한 모든 유효성 검사 메시지를 요약 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-147">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```csthml
<ValidationSummary />
```

<span data-ttu-id="cfd2c-148">`Model` 매개 변수를 사용 하 여 특정 모델에 대 한 유효성 검사 메시지를 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-148">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```csthml
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="cfd2c-149">`ValidationMessage` 구성 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 비슷한 특정 필드에 대 한 유효성 검사 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-149">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="cfd2c-150">`For` 특성과 모델 속성의 이름을 지정 하는 람다 식을 사용 하 여 유효성을 검사 하기 위한 필드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-150">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="cfd2c-151">`ValidationMessage` 및 `ValidationSummary` 구성 요소는 임의 특성을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-151">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="cfd2c-152">구성 요소 매개 변수와 일치 하지 않는 특성은 생성 된 `<div>` 또는 `<ul>` 요소에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-152">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="cfd2c-153">사용자 지정 유효성 검사 특성</span><span class="sxs-lookup"><span data-stu-id="cfd2c-153">Custom validation attributes</span></span>

<span data-ttu-id="cfd2c-154">[사용자 지정 유효성 검사 특성](xref:mvc/models/validation#custom-attributes)을 사용 하는 경우 유효성 검사 결과가 필드와 올바르게 연결 되도록 하려면 <xref:System.ComponentModel.DataAnnotations.ValidationResult>를 만들 때 유효성 검사 컨텍스트의 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-154">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a><span data-ttu-id="cfd2c-155">데이터 주석 유효성 검사 패키지 Blazor</span><span class="sxs-lookup"><span data-stu-id="cfd2c-155">Blazor data annotations validation package</span></span>

<span data-ttu-id="cfd2c-156">[AspNetCoreBlazor입니다. DataAnnotations. 유효성 검사](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) 는 `DataAnnotationsValidator` 구성 요소를 사용 하 여 유효성 검사 환경 간격을 채우는 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-156">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="cfd2c-157">패키지가 현재 *실험적*입니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-157">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="cfd2c-158">[CompareProperty] 특성</span><span class="sxs-lookup"><span data-stu-id="cfd2c-158">[CompareProperty] attribute</span></span>

<span data-ttu-id="cfd2c-159"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> `DataAnnotationsValidator` 구성 요소에서 제대로 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-159">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="cfd2c-160">[AspNetCoreBlazor입니다. DataAnnotations. 유효성 검사](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *실험적* 패키지는 이러한 제한을 해결 하는 추가 유효성 검사 특성 `ComparePropertyAttribute`을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-160">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="cfd2c-161">Blazor 앱에서 `[CompareProperty]`는 `[Compare]` 특성을 직접 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-161">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span> <span data-ttu-id="cfd2c-162">자세한 내용은 [Onvalidsubmit EditForm (aspnet/AspNetCore #10643)에서 Compareattribute를 무시](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748)하는 방법을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-162">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="cfd2c-163">중첩 된 모델, 컬렉션 형식 및 복합 형식</span><span class="sxs-lookup"><span data-stu-id="cfd2c-163">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="cfd2c-164">은 기본 제공 `DataAnnotationsValidator`에서 데이터 주석을 사용 하 여 양식 입력의 유효성을 검사 하는 기능을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-164"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="cfd2c-165">그러나 `DataAnnotationsValidator`는 컬렉션 또는 복합 형식 속성이 아닌 폼에 바인딩된 모델의 최상위 속성에 대해서만 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-165">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="cfd2c-166">컬렉션 및 복합 형식 속성을 포함 하 여 바인딩된 모델의 전체 개체 그래프의 유효성을 검사 하려면BlazorAspNetCore에서 *제공 하는* `ObjectGraphDataAnnotationsValidator`을 사용 합니다. [ DataAnnotations. 유효성 검사](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) 패키지:</span><span class="sxs-lookup"><span data-stu-id="cfd2c-166">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```cshtml
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="cfd2c-167">`[ValidateComplexType]`를 사용 하 여 모델 속성에 주석을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-167">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="cfd2c-168">다음 모델 클래스에서 `ShipDescription` 클래스에는 모델이 폼에 바인딩될 때 유효성을 검사 하기 위한 추가 데이터 주석이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-168">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="cfd2c-169">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfd2c-169">*Starship.cs*:</span></span>

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

<span data-ttu-id="cfd2c-170">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="cfd2c-170">*ShipDescription.cs*:</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="cfd2c-171">복합 형식 또는 컬렉션 형식 속성의 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="cfd2c-171">Validation of complex or collection type properties</span></span>

<span data-ttu-id="cfd2c-172">모델의 속성에 적용 되는 유효성 검사 특성은 폼이 제출 될 때 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-172">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="cfd2c-173">그러나 모델의 복합 데이터 형식 또는 컬렉션의 속성은 `DataAnnotationsValidator` 구성 요소에의 한 폼 전송에서 유효성이 검사 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-173">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="cfd2c-174">이 시나리오에서 중첩 된 유효성 검사 특성을 적용 하려면 사용자 지정 유효성 검사 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-174">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="cfd2c-175">예제는 [Blazor 유효성 검사 샘플 (aspnet/샘플)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cfd2c-175">For an example, see the [Blazor Validation sample (aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
