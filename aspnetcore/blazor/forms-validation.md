---
title: ASP.NET Core Blazor 폼 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: blazor/forms-validation
ms.openlocfilehash: c68ebf7f7bf07b6c243ab16307716cea13870446
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176349"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor 폼 및 유효성 검사

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

폼 및 유효성 검사는 [데이터 주석을](xref:mvc/models/validation)사용 하 여 Blazor에서 지원 됩니다.

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

양식은 `EditForm` 구성 요소를 사용 하 여 정의 됩니다. 다음 폼은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.

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

* 폼은 `ExampleModel` 형식에 정의 된 유효성 `name` 검사를 사용 하 여 필드에서 사용자 입력의 유효성을 검사 합니다. 모델은 구성 요소의 `@code` 블록에 생성 되 고 전용 필드 (`exampleModel`)에 저장 됩니다. 필드는 `Model` `<EditForm>` 요소의 특성에 할당 됩니다.
* 구성 `DataAnnotationsValidator` 요소는 데이터 주석을 사용 하 여 유효성 검사 지원을 연결 합니다.
* 구성 `ValidationSummary` 요소는 유효성 검사 메시지를 요약 합니다.
* `HandleValidSubmit`폼이 성공적으로 제출 되 면 트리거됩니다 (유효성 검사 통과).

사용자 입력을 수신 하 고 유효성을 검사 하는 데 사용할 수 있는 기본 제공 입력 구성 요소 집합입니다. 입력이 변경 되 고 양식이 제출 될 때 유효성 검사가 수행 됩니다. 사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.

| 입력 구성 요소 | 렌더링 됨&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

를 비롯 `EditForm`한 모든 입력 구성 요소는 임의 특성을 지원 합니다. 구성 요소 매개 변수와 일치 하지 않는 특성은 렌더링 된 HTML 요소에 추가 됩니다.

입력 구성 요소는 편집 시 유효성을 검사 하 고 CSS 클래스를 변경 하 여 필드 상태를 반영 하는 기본 동작을 제공 합니다. 일부 구성 요소는 유용한 구문 분석 논리를 포함 합니다. 예를 들어 `InputDate` 및 `InputNumber` 는 유효성 검사 오류로 등록 하 여 구문 분석할 수 값을 적절 하 게 처리 합니다. Null 값을 사용할 수 있는 형식은 대상 필드의 null 허용 여부도 지원 합니다 (예 `int?`:).

다음 `Starship` 형식은 이전 `ExampleModel`보다 더 큰 속성 및 데이터 주석 집합을 사용 하 여 유효성 검사 논리를 정의 합니다.

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

앞의 예제 `Description` 에서는 데이터 주석이 없으므로은 선택 사항입니다.

다음 폼은 `Starship` 모델에 정의 된 유효성 검사를 사용 하 여 사용자 입력의 유효성을 검사 합니다.

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

는 `EditForm` 수정 된 `EditContext` 필드와 현재 유효성 검사 메시지를 포함 하 여 편집 프로세스에 대 한 메타 데이터를 추적 하는를 [연계 값](xref:blazor/components#cascading-values-and-parameters) 으로 만듭니다. 또한 `EditForm` 는 유효한 전송 (`OnValidSubmit`, `OnInvalidSubmit`)에 대 한 편리한 이벤트를 제공 합니다. 또는를 사용 `OnSubmit` 하 여 유효성 검사를 트리거하고 사용자 지정 유효성 검사 코드를 사용 하 여 필드 값을 확인 합니다.

## <a name="inputtext-based-on-the-input-event"></a>입력 이벤트를 기반으로 하는 InputText

구성 요소를 사용 하 여 `change` 이벤트 대신 이벤트를 `input` 사용 하는 사용자 지정 구성 요소를 만듭니다. `InputText`

다음 태그를 사용 하 여 구성 요소를 만들고 사용 하는 것 `InputText` 과 같은 구성 요소를 사용 합니다.

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a>유효성 검사 지원

구성 `DataAnnotationsValidator` 요소는 데이터 주석을 사용 하 여 종속 `EditContext`된에 유효성 검사 지원을 연결 합니다. 데이터 주석을 사용 하 여 유효성 검사 지원을 활성화 하려면이 명시적인 제스처가 필요 합니다. 데이터 주석과 다른 유효성 검사 시스템을 사용 하려면를 사용자 지정 `DataAnnotationsValidator` 구현으로 바꿉니다. ASP.NET Core 구현은 참조 소스에서 검사할 수 있습니다. [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

구성 `ValidationSummary` 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 비슷한 모든 유효성 검사 메시지를 요약 합니다.

구성 `ValidationMessage` 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 비슷한 특정 필드에 대 한 유효성 검사 메시지를 표시 합니다. `For` 특성과 모델 속성의 이름을 지정 하는 람다 식을 사용 하 여 유효성을 검사 하기 위한 필드를 지정 합니다.

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

`ValidationMessage` 및`ValidationSummary` 구성 요소는 임의 특성을 지원 합니다. 구성 요소 매개 변수와 일치 하지 않는 특성이 생성 `<div>` 된 또는 `<ul>` 요소에 추가 됩니다.

### <a name="validation-of-complex-or-collection-type-properties"></a>복합 형식 또는 컬렉션 형식 속성의 유효성 검사

모델의 속성에 적용 되는 유효성 검사 특성은 폼이 제출 될 때 유효성을 검사 합니다. 그러나 모델의 복합 데이터 형식 또는 컬렉션의 속성은 양식 전송에서 유효성이 검사 되지 않습니다. 이 시나리오에서 중첩 된 유효성 검사 특성을 적용 하려면 사용자 지정 유효성 검사 구성 요소를 사용 합니다. 예제는 [aspnet/Samples GitHub 리포지토리에서 Blazor Validation 샘플](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)을 참조 하세요.
