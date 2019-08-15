---
title: ASP.NET Core Blazor 폼 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030338"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="57ec5-103">ASP.NET Core Blazor 폼 및 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="57ec5-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="57ec5-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="57ec5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="57ec5-105">폼 및 유효성 검사는 [데이터 주석을](xref:mvc/models/validation)사용 하 여 Blazor에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="57ec5-106">양식 및 유효성 검사 시나리오는 각 미리 보기 릴리스에서 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="57ec5-107">다음 `ExampleModel` 형식은 데이터 주석을 사용 하 여 유효성 검사 논리를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="57ec5-108">양식은 `EditForm` 구성 요소를 사용 하 여 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="57ec5-109">다음 폼은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="57ec5-110">폼은 `ExampleModel` 형식에 정의 된 유효성 `name` 검사를 사용 하 여 필드에서 사용자 입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="57ec5-111">모델은 구성 요소의 `@code` 블록에 생성 되 고 전용 필드 (`exampleModel`)에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="57ec5-112">필드는 `Model` `<EditForm>` 요소의 특성에 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="57ec5-113">구성 `DataAnnotationsValidator` 요소는 데이터 주석을 사용 하 여 유효성 검사 지원을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="57ec5-114">구성 `ValidationSummary` 요소는 유효성 검사 메시지를 요약 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="57ec5-115">`HandleValidSubmit`폼이 성공적으로 제출 되 면 트리거됩니다 (유효성 검사 통과).</span><span class="sxs-lookup"><span data-stu-id="57ec5-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="57ec5-116">사용자 입력을 수신 하 고 유효성을 검사 하는 데 사용할 수 있는 기본 제공 입력 구성 요소 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="57ec5-117">입력이 변경 되 고 양식이 제출 될 때 유효성 검사가 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="57ec5-118">사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="57ec5-119">입력 구성 요소</span><span class="sxs-lookup"><span data-stu-id="57ec5-119">Input component</span></span> | <span data-ttu-id="57ec5-120">렌더링 됨&hellip;</span><span class="sxs-lookup"><span data-stu-id="57ec5-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="57ec5-121">를 비롯 `EditForm`한 모든 입력 구성 요소는 임의 특성을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="57ec5-122">구성 요소 매개 변수와 일치 하지 않는 특성은 렌더링 된 HTML 요소에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="57ec5-123">입력 구성 요소는 편집 시 유효성을 검사 하 고 CSS 클래스를 변경 하 여 필드 상태를 반영 하는 기본 동작을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="57ec5-124">일부 구성 요소는 유용한 구문 분석 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="57ec5-125">예를 들어 `InputDate` 및 `InputNumber` 는 유효성 검사 오류로 등록 하 여 구문 분석할 수 값을 적절 하 게 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="57ec5-126">Null 값을 사용할 수 있는 형식은 대상 필드의 null 허용 여부도 지원 합니다 (예 `int?`:).</span><span class="sxs-lookup"><span data-stu-id="57ec5-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="57ec5-127">다음 `Starship` 형식은 이전 `ExampleModel`보다 더 큰 속성 및 데이터 주석 집합을 사용 하 여 유효성 검사 논리를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="57ec5-128">앞의 예제 `Description` 에서는 데이터 주석이 없으므로은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="57ec5-129">다음 폼은 `Starship` 모델에 정의 된 유효성 검사를 사용 하 여 사용자 입력의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="57ec5-130">는 `EditForm` 수정 된 `EditContext` 필드와 현재 유효성 검사 메시지를 포함 하 여 편집 프로세스에 대 한 메타 데이터를 추적 하는를 [연계 값](xref:blazor/components#cascading-values-and-parameters) 으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="57ec5-131">또한 `EditForm` 는 유효한 전송 (`OnValidSubmit`, `OnInvalidSubmit`)에 대 한 편리한 이벤트를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="57ec5-132">또는를 사용 `OnSubmit` 하 여 유효성 검사를 트리거하고 사용자 지정 유효성 검사 코드를 사용 하 여 필드 값을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="57ec5-133">구성 `DataAnnotationsValidator` 요소는 데이터 주석을 사용 하 여 종속 `EditContext`된에 유효성 검사 지원을 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="57ec5-134">데이터 주석을 사용 하 여 유효성 검사 지원을 사용 하도록 설정 하는 것은 현재이 명시적인 제스처가 필요 하지만이를 재정의할 수 있는 기본 동작으로 설정 하는 것을 고려 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="57ec5-135">데이터 주석과 다른 유효성 검사 시스템을 사용 하려면를 사용자 지정 `DataAnnotationsValidator` 구현으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="57ec5-136">ASP.NET Core 구현은 참조 소스에서 검사할 수 있습니다. [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="57ec5-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="57ec5-137">*ASP.NET Core 구현에는 미리 보기 릴리스 기간 동안 신속한 업데이트가 적용 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="57ec5-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="57ec5-138">구성 `ValidationSummary` 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 비슷한 모든 유효성 검사 메시지를 요약 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="57ec5-139">구성 `ValidationMessage` 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 비슷한 특정 필드에 대 한 유효성 검사 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="57ec5-140">`For` 특성과 모델 속성의 이름을 지정 하는 람다 식을 사용 하 여 유효성을 검사 하기 위한 필드를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="57ec5-141">`ValidationMessage` 및`ValidationSummary` 구성 요소는 임의 특성을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="57ec5-142">구성 요소 매개 변수와 일치 하지 않는 특성이 생성 `<div>` 된 또는 `<ul>` 요소에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57ec5-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>
