---
title: ASP.NET Core Blazor 폼 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 4531ef44a7df3951f3bebdf88e597165fa75f06e
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310324"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="21592-103">ASP.NET Core Blazor 폼 및 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="21592-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="21592-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="21592-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="21592-105">폼 및 유효성 검사는 [데이터 주석을](xref:mvc/models/validation)사용 하 여 Blazor에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21592-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="21592-106">양식 및 유효성 검사 시나리오는 각 미리 보기 릴리스에서 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21592-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="21592-107">다음 `ExampleModel` 형식은 데이터 주석을 사용 하 여 유효성 검사 논리를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="21592-108">양식은 `EditForm` 구성 요소를 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21592-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="21592-109">다음 폼은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="21592-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

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

* <span data-ttu-id="21592-110">폼은 `ExampleModel` 형식에 정의 된 유효성 `name` 검사를 사용 하 여 필드에서 사용자 입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="21592-111">모델은 구성 요소의 `@code` 블록에 생성 되 고 전용 필드 (`exampleModel`)에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21592-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="21592-112">필드는 `Model` `<EditForm>` 요소의 특성에 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21592-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="21592-113">구성 `DataAnnotationsValidator` 요소는 데이터 주석을 사용 하 여 유효성 검사 지원을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="21592-114">구성 `ValidationSummary` 요소는 유효성 검사 메시지를 요약 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="21592-115">`HandleValidSubmit`폼이 성공적으로 제출 되 면 트리거됩니다 (유효성 검사 통과).</span><span class="sxs-lookup"><span data-stu-id="21592-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="21592-116">사용자 입력을 수신 하 고 유효성을 검사 하는 데 사용할 수 있는 기본 제공 입력 구성 요소 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="21592-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="21592-117">입력이 변경 되 고 양식이 제출 될 때 유효성 검사가 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21592-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="21592-118">사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21592-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="21592-119">입력 구성 요소</span><span class="sxs-lookup"><span data-stu-id="21592-119">Input component</span></span> | <span data-ttu-id="21592-120">렌더링 됨&hellip;</span><span class="sxs-lookup"><span data-stu-id="21592-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="21592-121">를 비롯 `EditForm`한 모든 입력 구성 요소는 임의 특성을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="21592-122">구성 요소 매개 변수와 일치 하지 않는 특성은 렌더링 된 HTML 요소에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21592-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="21592-123">입력 구성 요소는 편집 시 유효성을 검사 하 고 CSS 클래스를 변경 하 여 필드 상태를 반영 하는 기본 동작을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="21592-124">일부 구성 요소는 유용한 구문 분석 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="21592-125">예를 들어 `InputDate` 및 `InputNumber` 는 유효성 검사 오류로 등록 하 여 구문 분석할 수 값을 적절 하 게 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="21592-126">Null 값을 사용할 수 있는 형식은 대상 필드의 null 허용 여부도 지원 합니다 (예 `int?`:).</span><span class="sxs-lookup"><span data-stu-id="21592-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="21592-127">다음 `Starship` 형식은 이전 `ExampleModel`보다 더 큰 속성 및 데이터 주석 집합을 사용 하 여 유효성 검사 논리를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="21592-128">앞의 예제 `Description` 에서는 데이터 주석이 없으므로은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="21592-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="21592-129">다음 폼은 `Starship` 모델에 정의 된 유효성 검사를 사용 하 여 사용자 입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="21592-130">는 `EditForm` 수정 된 `EditContext` 필드와 현재 유효성 검사 메시지를 포함 하 여 편집 프로세스에 대 한 메타 데이터를 추적 하는를 [연계 값](xref:blazor/components#cascading-values-and-parameters) 으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="21592-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="21592-131">또한 `EditForm` 는 유효한 전송 (`OnValidSubmit`, `OnInvalidSubmit`)에 대 한 편리한 이벤트를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="21592-132">또는를 사용 `OnSubmit` 하 여 유효성 검사를 트리거하고 사용자 지정 유효성 검사 코드를 사용 하 여 필드 값을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="21592-133">입력 이벤트를 기반으로 하는 InputText</span><span class="sxs-lookup"><span data-stu-id="21592-133">InputText based on the input event</span></span>

<span data-ttu-id="21592-134">구성 요소를 사용 하 여 `change` 이벤트 대신 이벤트를 `input` 사용 하는 사용자 지정 구성 요소를 만듭니다. `InputText`</span><span class="sxs-lookup"><span data-stu-id="21592-134">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="21592-135">다음 태그를 사용 하 여 구성 요소를 만들고 사용 하는 것 `InputText` 과 같은 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-135">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="21592-136">유효성 검사 지원</span><span class="sxs-lookup"><span data-stu-id="21592-136">Validation support</span></span>

<span data-ttu-id="21592-137">구성 `DataAnnotationsValidator` 요소는 데이터 주석을 사용 하 여 종속 `EditContext`된에 유효성 검사 지원을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-137">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="21592-138">데이터 주석을 사용 하 여 유효성 검사 지원을 사용 하도록 설정 하는 것은 현재이 명시적인 제스처가 필요 하지만이를 재정의할 수 있는 기본 동작으로 설정 하는 것을 고려 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="21592-138">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="21592-139">데이터 주석과 다른 유효성 검사 시스템을 사용 하려면를 사용자 지정 `DataAnnotationsValidator` 구현으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="21592-139">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="21592-140">ASP.NET Core 구현은 참조 소스에서 검사할 수 있습니다. [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="21592-140">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="21592-141">*ASP.NET Core 구현에는 미리 보기 릴리스 기간 동안 신속한 업데이트가 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="21592-141">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="21592-142">구성 `ValidationSummary` 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 비슷한 모든 유효성 검사 메시지를 요약 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-142">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="21592-143">구성 `ValidationMessage` 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 비슷한 특정 필드에 대 한 유효성 검사 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-143">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="21592-144">`For` 특성과 모델 속성의 이름을 지정 하는 람다 식을 사용 하 여 유효성을 검사 하기 위한 필드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-144">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="21592-145">`ValidationMessage` 및`ValidationSummary` 구성 요소는 임의 특성을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-145">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="21592-146">구성 요소 매개 변수와 일치 하지 않는 특성이 생성 `<div>` 된 또는 `<ul>` 요소에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="21592-146">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="21592-147">복합 형식 또는 컬렉션 형식 속성의 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="21592-147">Validation of complex or collection type properties</span></span>

<span data-ttu-id="21592-148">모델의 속성에 적용 되는 유효성 검사 특성은 폼이 제출 될 때 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-148">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="21592-149">그러나 모델의 복합 데이터 형식 또는 컬렉션의 속성은 양식 전송에서 유효성이 검사 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="21592-149">However, the properties of collections or complex data types of a model aren't validated on form submission.</span></span> <span data-ttu-id="21592-150">이 시나리오에서 중첩 된 유효성 검사 특성을 적용 하려면 사용자 지정 유효성 검사 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="21592-150">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="21592-151">예제는 [aspnet/Samples GitHub 리포지토리에서 Blazor Validation 샘플](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="21592-151">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>
