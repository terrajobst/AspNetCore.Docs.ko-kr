---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 데이터 모델 - 5/8
author: rick-anderson
description: 이 자습서에서는 더 많은 엔터티 및 관계를 추가하고, 서식 지정, 유효성 검사 및 매핑 규칙을 지정하여 데이터 모델을 사용자 지정합니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 96e4acf0d6c9c079ebee32fc2f9951fdd668931b
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914967"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="73e31-103">ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 데이터 모델 - 5/8</span><span class="sxs-lookup"><span data-stu-id="73e31-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="73e31-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="73e31-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73e31-105">이전 자습서에서는 세 가지 엔터티로 구성된 기본 데이터 모델을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="73e31-106">이 자습서에서:</span><span class="sxs-lookup"><span data-stu-id="73e31-106">In this tutorial:</span></span>

* <span data-ttu-id="73e31-107">더 많은 엔터티 및 관계가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="73e31-108">데이터 모델은 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하여 사용자 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="73e31-109">다음 그림에서는 완료된 데이터 모델을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-109">The completed data model is shown in the following illustration:</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="73e31-111">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-111">The Student entity</span></span>

![학생 엔터티](complex-data-model/_static/student-entity.png)

<span data-ttu-id="73e31-113">*Models/Student.cs*에 있는 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="73e31-114">앞의 코드는 `FullName` 속성을 추가하고 기존 속성에 다음 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="73e31-115">FullName 계산된 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-115">The FullName calculated property</span></span>

<span data-ttu-id="73e31-116">`FullName`은 다른 두 개의 속성을 연결하여 생성되는 값을 반환하는 계산된 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="73e31-117">`FullName`은 설정될 수 없으므로 get 접근자만 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="73e31-118">데이터베이스에서 `FullName` 열이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="73e31-119">DataType 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="73e31-120">학생 등록 날짜의 경우 해당 날짜만 관련이 있지만 모든 페이지에는 현재 해당 날짜와 함께 하루의 시간이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="73e31-121">데이터 주석 특성을 사용하면 데이터를 표시하는 모든 페이지에서 표시 형식을 해결하는 하나의 코드 변경을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="73e31-122">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="73e31-123">이 경우 날짜 및 시간이 아닌 날짜만 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="73e31-124">[DataType 열거형](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)은 날짜, 시간, 전화 번호, 통화, 이메일 주소 등과 같은 많은 데이터 형식을 제공합니다. `DataType` 특성을 통해 앱에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="73e31-125">예:</span><span class="sxs-lookup"><span data-stu-id="73e31-125">For example:</span></span>

* <span data-ttu-id="73e31-126">`mailto:` 링크는 `DataType.EmailAddress`에 대해 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="73e31-127">날짜 선택기는 대부분의 브라우저에서 `DataType.Date`에 대해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="73e31-128">`DataType` 특성은 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="73e31-129">`DataType` 특성은 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="73e31-130">DisplayFormat 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="73e31-131">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="73e31-132">기본적으로 날짜 필드는 서버의 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)를 기본으로 하는 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="73e31-133">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="73e31-134">`ApplyFormatInEditMode` 설정은 서식 지정이 편집 UI에도 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="73e31-135">일부 필드는 `ApplyFormatInEditMode`를 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="73e31-136">예를 들어 통화 기호는 일반적으로 편집 텍스트 상자에 표시되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="73e31-137">`DisplayFormat` 특성은 단독으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="73e31-138">일반적으로 `DisplayFormat` 특성과 함께 `DataType` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="73e31-139">`DataType` 특성은 화면에서 렌더링하는 방법과 대조적으로 데이터의 의미 체계를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="73e31-140">`DataType` 특성은 `DisplayFormat`에서 사용할 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="73e31-141">브라우저는 HTML5 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="73e31-142">예를 들어 달력 컨트롤, 로캘에 적합한 통화 기호, 메일 링크, 클라이언트 쪽 입력 유효성 검사를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="73e31-143">기본적으로 브라우저는 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="73e31-144">자세한 내용은 [\<입력> 태그 도우미 설명서](xref:mvc/views/working-with-forms#the-input-tag-helper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="73e31-145">StringLength 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="73e31-146">데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지는 특성으로 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="73e31-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 특성은 데이터 필드에서 허용되는 최소 및 최대 문자 길이를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="73e31-148">표시된 코드는 이름을 최대 50자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="73e31-149">최소 문자열 길이를 설정하는 예제는 [뒷부분](#the-required-attribute)에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="73e31-150">`StringLength` 특성은 또한 클라이언트 쪽 및 서버 쪽 유효성 검사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="73e31-151">최소값은 데이터베이스 스키마에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="73e31-152">`StringLength` 특성은 이름에 공백을 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="73e31-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 특성은 입력에 제한을 적용하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="73e31-154">예를 들어 다음 코드는 첫 번째 문자가 대문자여야 하고, 나머지 문자는 알파벳순이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73e31-156">**SQL Server 개체 탐색기**(SSOX)에서 **학생** 테이블을 두 번 클릭하여 학생 테이블 디자이너를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![마이그레이션 전 SSOX의 학생 테이블](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="73e31-158">위의 이미지는 `Student` 테이블에 대한 스키마를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="73e31-159">이름 필드의 형식은 `nvarchar(MAX)`입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="73e31-160">이 자습서의 뒷부분에서 마이그레이션을 만들고 적용하면 문자열 길이 특성의 결과로 이름 필드가 `nvarchar(50)`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73e31-162">SQLite 도구에서 `Student` 테이블에 대한 열 정의를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="73e31-163">이름 필드의 형식은 `Text`입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-163">The name fields have type `Text`.</span></span> <span data-ttu-id="73e31-164">이름 필드를 `FirstMidName`이라고 하는 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="73e31-165">다음 섹션에서는 해당 열의 이름을 `FirstName`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="73e31-166">열 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="73e31-167">특성은 클래스 및 속성이 데이터베이스에 매핑되는 방법을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="73e31-168">`Student` 모델에서 `Column` 특성은 데이터베이스에서 `FirstMidName` 속성의 이름을 “FirstName”에 매핑하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="73e31-169">데이터베이스가 만들어질 때 모델의 속성 이름은 열 이름에 사용됩니다(`Column` 특성이 사용되는 경우 제외).</span><span class="sxs-lookup"><span data-stu-id="73e31-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="73e31-170">필드에 중간 이름도 포함될 수 있으므로 `Student` 모델은 첫 번째 이름 필드에 대해 `FirstMidName`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="73e31-171">`[Column]` 특성을 사용하면 데이터 모델의 `Student.FirstMidName`은 `Student` 테이블의 `FirstName` 열에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="73e31-172">`Column` 특성을 추가하면 `SchoolContext`를 지원하는 모델이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="73e31-173">`SchoolContext`를 지원하는 모델은 데이터베이스와 더 이상 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="73e31-174">해당 불일치는 이 자습서의 뒷부분에서 마이그레이션을 추가하여 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="73e31-175">필수 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="73e31-176">`Required` 특성에서 이름 속성은 필수 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="73e31-177">`Required` 특성은 값 형식(예: `DateTime`, `int` 및 `double`)과 같은 비 nullable 형식에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="73e31-178">Null일 수 없는 형식은 자동으로 필수 필드로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="73e31-179">`Required` 특성은 `StringLength` 특성에서 최소 길이 매개 변수로 대체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-179">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="73e31-180">표시 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-180">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="73e31-181">`Display` 특성은 텍스트 상자에 대한 캡션이 "First Name", "Last Name", "Full Name" 및 "Enrollment Date"여야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-181">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="73e31-182">기본 캡션에는 단어를 분할하는 공백이 없습니다(예: "Lastname)".</span><span class="sxs-lookup"><span data-stu-id="73e31-182">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="73e31-183">마이그레이션 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-183">Create a migration</span></span>

<span data-ttu-id="73e31-184">앱을 실행하고 [학생] 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-184">Run the app and go to the Students page.</span></span> <span data-ttu-id="73e31-185">예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-185">An exception is thrown.</span></span> <span data-ttu-id="73e31-186">`[Column]` 특성을 사용하면 EF가 `FirstName` 열을 찾아야 하지만 데이터베이스의 열 이름은 `FirstMidName`입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-186">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-187">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73e31-188">다음 예제와 유사한 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-188">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="73e31-189">PMC에서 다음 명령을 입력하여 새 마이그레이션을 만들고 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-189">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="73e31-190">이 명령 중 첫 번째는 다음 경고 메시지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-190">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="73e31-191">이름 필드는 이제 50자로 제한되기 때문에 경고가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-191">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="73e31-192">데이터베이스의 이름에 50자 이상의 문자가 있는 경우 51자부터 마지막 문자까지 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-192">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="73e31-193">SSOX에서 학생 테이블을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-193">Open the Student table in SSOX:</span></span>

  ![마이그레이션 후 SSOX의 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="73e31-195">마이그레이션이 적용되기 전에 이름 열의 형식은 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)이었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-195">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="73e31-196">이름 열은 이제 `nvarchar(50)`입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-196">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="73e31-197">열 이름은 `FirstMidName`에서 `FirstName`으로 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-197">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73e31-199">다음 예제와 유사한 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-199">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="73e31-200">프로젝트 폴더의 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-200">Open a command window in the project folder.</span></span> <span data-ttu-id="73e31-201">다음 명령을 입력하여 새 마이그레이션을 만들고 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-201">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="73e31-202">데이터베이스 업데이트 명령은 다음 예제와 같은 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-202">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="73e31-203">이 자습서에서 이 오류를 무시하는 방법은 초기 마이그레이션을 삭제하고 다시 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-203">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="73e31-204">자세한 내용은 [마이그레이션 자습서](xref:data/ef-rp/migrations)의 맨 위에 있는 SQLite 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-204">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="73e31-205">*Migrations* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-205">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="73e31-206">다음 명령을 실행하여 데이터베이스를 삭제하고, 새 초기 마이그레이션을 만들고, 마이그레이션을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-206">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="73e31-207">SQLite 도구에서 Student 테이블을 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-207">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="73e31-208">이전에 FirstMidName이었던 열은 이제 FirstName입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-208">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="73e31-209">앱을 실행하고 [학생] 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-209">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="73e31-210">시간이 날짜와 함께 입력되거나 표시되지 않는 것을 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-210">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="73e31-211">**새로 만들기**를 선택하고, 50자보다 긴 이름을 입력해 보세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-211">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="73e31-212">다음 섹션의 일부 단계에서 앱 빌드는 컴파일러 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-212">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="73e31-213">지침은 앱을 빌드하는 시기를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-213">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="73e31-214">Instructor 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-214">The Instructor Entity</span></span>

![강사 엔터티](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="73e31-216">다음 코드로 *Models/Instructor.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-216">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="73e31-217">한 줄에 여러 특성이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-217">Multiple attributes can be on one line.</span></span> <span data-ttu-id="73e31-218">`HireDate` 특성은 다음과 같이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-218">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="73e31-219">탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-219">Navigation properties</span></span>

<span data-ttu-id="73e31-220">`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-220">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="73e31-221">강사는 여러 강좌를 가르칠 수 있으므로 `CourseAssignments`는 컬렉션으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-221">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="73e31-222">강사는 최대 하나의 사무실을 가질 수 있으므로 `OfficeAssignment` 속성은 단일 `OfficeAssignment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-222">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="73e31-223">사무실이 할당되지 않은 경우 `OfficeAssignment`는 Null입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-223">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="73e31-224">OfficeAssignment 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-224">The OfficeAssignment entity</span></span>

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="73e31-226">다음 코드로 *Models/OfficeAssignment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-226">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="73e31-227">키 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-227">The Key attribute</span></span>

<span data-ttu-id="73e31-228">`[Key]` 특성은 속성 이름이 classnameID 또는 ID가 아닌 다른 것일 때 PK(기본 키)로 속성을 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-228">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="73e31-229">`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-229">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="73e31-230">사무실 할당은 할당된 강사와 관련하여 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-230">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="73e31-231">`OfficeAssignment` PK는 `Instructor` 엔터티에 대한 해당 FK(외래 키)이기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-231">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="73e31-232">`InstructorID`는 ID 또는 classnameID 명명 규칙을 따르지 않으므로 EF Core는 `InstructorID`를 `OfficeAssignment`의 PK로 자동으로 인식할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-232">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="73e31-233">따라서 `Key` 특성은 PK로 `InstructorID`를 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="73e31-234">기본적으로 EF Core는 열이 관계 확인을 위한 것이기 때문에 키를 데이터베이스에서 생성되지 않은 것으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="73e31-235">강사 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-235">The Instructor navigation property</span></span>

<span data-ttu-id="73e31-236">지정된 강사에 대한 `OfficeAssignment` 행이 없을 수 있으므로 `Instructor.OfficeAssignment` 탐색 속성은 Null일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-236">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="73e31-237">강사는 사무실 할당이 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-237">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="73e31-238">외래 키 `InstructorID` 형식이 비 nullable 값 형식인 `int`이므로 `OfficeAssignment.Instructor` 탐색 속성은 항상 강사 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-238">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="73e31-239">사무실 할당은 강사 없이 존재할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-239">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="73e31-240">`Instructor` 엔터티에 관련된 `OfficeAssignment` 엔터티가 있는 경우 각 엔터티는 해당 탐색 속성의 다른 엔터티에 대한 참조를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-240">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="73e31-241">Course 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-241">The Course Entity</span></span>

![강좌 엔터티](complex-data-model/_static/course-entity.png)

<span data-ttu-id="73e31-243">*Models/Course.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-243">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="73e31-244">`Course` 엔터티에는 FK(외래 키) 속성 `DepartmentID`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-244">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="73e31-245">`DepartmentID`는 관련된 `Department` 엔터티를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-245">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="73e31-246">`Course` 엔터티에는 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-246">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="73e31-247">모델에 관련된 엔터티에 대한 탐색 속성이 있는 경우 EF Core는 데이터 모델에 대한 외래 키 속성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-247">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="73e31-248">EF Core는 필요한 어디든지 데이터베이스에 FK를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-248">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="73e31-249">EF Core는 자동으로 만들어진 FK에 대한 [섀도 속성](/ef/core/modeling/shadow-properties)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-249">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="73e31-250">그러나 데이터 모델에 FK를 명시적으로 포함하면 더 간단하고 더 효율적으로 업데이트를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-250">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="73e31-251">예를 들어 FK 키 속성 `DepartmentID`가 포함되지 *않은* 모델을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-251">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="73e31-252">과정 엔터티가 편집을 위해 페치되는 경우:</span><span class="sxs-lookup"><span data-stu-id="73e31-252">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="73e31-253">`Department` 속성은 명시적으로 로드되지 않은 경우 Null입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-253">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="73e31-254">과정 엔터티를 업데이트하려면 `Department` 엔터티를 먼저 페치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-254">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="73e31-255">FK 속성 `DepartmentID`가 데이터 모델에 포함된 경우 업데이트하기 전에 `Department` 엔터티를 페치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-255">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="73e31-256">DatabaseGenerated 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-256">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="73e31-257">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 특성은 PK가 데이터베이스에서 생성되지 않고 애플리케이션에서 제공되는 것을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-257">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="73e31-258">기본적으로 EF Core는 PK 값이 데이터베이스에서 생성되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-258">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="73e31-259">일반적으로 데이터베이스에서 생성된 PK 값을 사용하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-259">Database-generated is generally the best approach.</span></span> <span data-ttu-id="73e31-260">`Course` 엔터티의 경우 사용자는 PK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-260">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="73e31-261">예를 들어 수학 부서에 대한 1000 시리즈, 영어 부서에 대한 2000 시리즈와 같은 강좌 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-261">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="73e31-262">`DatabaseGenerated` 특성은 기본 값을 생성하는 데 사용될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-262">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="73e31-263">예를 들어 데이터베이스는 행이 만들어지거나 업데이트된 날짜를 기록하기 위해 날짜 필드를 자동으로 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-263">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="73e31-264">자세한 내용은 [생성된 속성](/ef/core/modeling/generated-properties)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-264">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="73e31-265">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-265">Foreign key and navigation properties</span></span>

<span data-ttu-id="73e31-266">`Course` 엔터티의 FK(외래 키) 속성 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-266">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="73e31-267">강좌는 하나의 부서에 할당되었으므로 `DepartmentID` FK 및 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-267">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="73e31-268">강좌에는 등록된 학생이 여러 명 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-268">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="73e31-269">여러 강사가 한 강좌를 수업할 수 있으므로 `CourseAssignments` 탐색 속성은 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-269">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="73e31-270">`CourseAssignment`는 [나중에](#many-to-many-relationships) 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-270">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="73e31-271">Department 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-271">The Department entity</span></span>

![부서 엔터티](complex-data-model/_static/department-entity.png)

<span data-ttu-id="73e31-273">다음 코드로 *Models/Department.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-273">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="73e31-274">열 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-274">The Column attribute</span></span>

<span data-ttu-id="73e31-275">이전에 `Column` 특성은 열 이름 매핑을 변경하는 데 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-275">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="73e31-276">`Department` 엔터티에 대한 코드에서 `Column` 특성은 SQL 데이터 형식 매핑을 변경하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-276">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="73e31-277">`Budget` 열은 데이터베이스에서 SQL Server money 형식을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-277">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="73e31-278">열 매핑은 일반적으로 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-278">Column mapping is generally not required.</span></span> <span data-ttu-id="73e31-279">EF Core는 속성에 대한 CLR 형식에 따라 적절한 SQL Server 데이터 형식을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-279">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="73e31-280">CLR `decimal` 형식은 SQL Server `decimal` 유형에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-280">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="73e31-281">`Budget`은 통화에 대한 것이고 money 데이터 형식은 통화에 더욱 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-281">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="73e31-282">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-282">Foreign key and navigation properties</span></span>

<span data-ttu-id="73e31-283">FK 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-283">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="73e31-284">부서는 관리자를 갖거나 갖지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-284">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="73e31-285">관리자는 항상 강사입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-285">An administrator is always an instructor.</span></span> <span data-ttu-id="73e31-286">따라서 `InstructorID` 속성은 `Instructor` 엔터티에 FK로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-286">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="73e31-287">탐색 속성이 `Administrator`로 명명되나, `Instructor` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-287">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="73e31-288">위의 코드에서 물음표(?)는 속성이 nullable임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-288">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="73e31-289">부서에는 강좌가 많이 있을 수 있으므로 강좌 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-289">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="73e31-290">규칙에 따라 EF Core는 Null을 허용하지 않는 FK 및 다대다 관계에 대한 계단식 삭제를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-290">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="73e31-291">이 기본 동작으로 인해 순환 계단식 삭제 규칙이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-291">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="73e31-292">순환 계단식 삭제 규칙은 마이그레이션이 추가될 때 예외를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-292">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="73e31-293">예를 들어 `Department.InstructorID` 속성이 비 nullable로 정의된 경우 EF Core는 계단식 삭제 규칙을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-293">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="73e31-294">이 경우 관리자로 할당된 강사가 삭제되면 부서가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-294">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="73e31-295">이 시나리오에서는 제한 규칙을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-295">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="73e31-296">다음 흐름 API는 제한 규칙을 설정하고 계단식 삭제를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-296">The following fluent API would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="73e31-297">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-297">The Enrollment entity</span></span>

<span data-ttu-id="73e31-298">등록 레코드는 한 명의 학생이 수행하는 하나의 강좌에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-298">An enrollment record is for one course taken by one student.</span></span>

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="73e31-300">*Models/Enrollment.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-300">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="73e31-301">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-301">Foreign key and navigation properties</span></span>

<span data-ttu-id="73e31-302">FK 속성 및 탐색 속성은 다음 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-302">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="73e31-303">등록 레코드는 하나의 강좌에 해당하므로 `CourseID` FK 속성 및 `Course` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-303">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="73e31-304">등록 레코드는 한 명의 학생에 해당하므로 `StudentID` FK 속성 및 `Student` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-304">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="73e31-305">다대다 관계</span><span class="sxs-lookup"><span data-stu-id="73e31-305">Many-to-Many Relationships</span></span>

<span data-ttu-id="73e31-306">`Student` 및 `Course` 엔터티 사이에 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-306">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="73e31-307">`Enrollment` 엔터티는 데이터베이스에서 *페이로드를 사용하여* 다대다 조인 테이블로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-307">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="73e31-308">"페이로드 사용"은 `Enrollment` 테이블이 조인된 테이블에 대한 FK 외에도 추가 데이터를 포함하는 것을 의미합니다(이 경우 PK 및 `Grade`).</span><span class="sxs-lookup"><span data-stu-id="73e31-308">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="73e31-309">다음 그림은 이러한 관계 모양을 엔터티 다이어그램으로 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-309">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="73e31-310">(이 다이어그램은 EF 6.x에 대한 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)를 사용하여 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-310">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="73e31-311">다이어그램 만들기는 자습서의 일부가 아닙니다.)</span><span class="sxs-lookup"><span data-stu-id="73e31-311">Creating the diagram isn't part of the tutorial.)</span></span>

![학생-강좌 다대다 관계](complex-data-model/_static/student-course.png)

<span data-ttu-id="73e31-313">각 관계 줄에는 한쪽 끝에 1, 다른 한쪽 끝에는 별표(\*)가 있으며, 이는 일대다 관계를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-313">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="73e31-314">`Enrollment` 테이블에 등급 정보가 포함되지 않은 경우 두 개의 FK(`CourseID` 및 `StudentID`)를 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-314">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="73e31-315">페이로드가 없는 다대다 조인 테이블은 PJT(순수 조인 테이블)라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-315">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="73e31-316">`Instructor` 및 `Course` 엔터티에는 순수 조인 테이블을 사용하는 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-316">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="73e31-317">참고: EF 6.x는 다대다 관계에 대한 암시적 조인 테이블을 지원하지만 EF Core는 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-317">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="73e31-318">자세한 내용은 [EF Core 2.0에서 다대다 관계](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-318">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="73e31-319">CourseAssignment 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-319">The CourseAssignment entity</span></span>

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="73e31-321">다음 코드로 *Models/CourseAssignment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-321">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="73e31-322">강사-과정 다대다 관계에는 조인 테이블이 필요하며, 해당 조인 테이블의 엔터티는 CourseAssignment입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-322">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![강사-강좌 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="73e31-324">조인 엔터티 `EntityName1EntityName2`의 이름을 지정하는 데 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-324">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="73e31-325">예를 들어 이 패턴을 사용하는 강사-과정 조인 테이블은 `CourseInstructor`입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-325">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="73e31-326">그러나 관계를 설명하는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-326">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="73e31-327">데이터 모델은 단순하게 시작하고 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-327">Data models start out simple and grow.</span></span> <span data-ttu-id="73e31-328">페이로드 없는 조인 테이블(PJT)은 페이로드를 포함하도록 자주 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-328">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="73e31-329">설명이 포함된 엔터티 이름으로 시작하여 이름은 조인 테이블이 변경될 때 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-329">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="73e31-330">이상적으로 조인 엔터티는 비즈니스 도메인에서 고유의 자연스러운(가능한 한 단어) 이름을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-330">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="73e31-331">예를 들어 Books 및 Customers는 Ratings라는 조인 엔터티를 통해 연결될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-331">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="73e31-332">강사-강좌 다대다 관계의 경우 `CourseAssignment`는 `CourseInstructor`보다 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-332">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="73e31-333">복합 키</span><span class="sxs-lookup"><span data-stu-id="73e31-333">Composite key</span></span>

<span data-ttu-id="73e31-334">`CourseAssignment`에서 두 개의 FK(`InstructorID` 및 `CourseID`)는 함께 `CourseAssignment` 테이블의 각 행을 고유하게 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-334">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="73e31-335">`CourseAssignment`는 전용 PK가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-335">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="73e31-336">`InstructorID` 및 `CourseID` 속성은 복합 PK로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-336">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="73e31-337">복합 PK를 EF Core로 지정하는 유일한 방법은 *흐름 API*를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-337">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="73e31-338">다음 섹션에서는 복합 PK를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-338">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="73e31-339">복합 키를 사용하면 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-339">The composite key ensures that:</span></span>

* <span data-ttu-id="73e31-340">하나의 강좌에 대해 여러 행이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-340">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="73e31-341">한 명의 강사에 대해 여러 행이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-341">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="73e31-342">동일한 강사 및 과정에 대한 여러 행은 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-342">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="73e31-343">`Enrollment` 조인 엔터티는 고유한 PK를 정의하므로 이러한 종류의 중복이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-343">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="73e31-344">이러한 중복 항목을 방지하려면:</span><span class="sxs-lookup"><span data-stu-id="73e31-344">To prevent such duplicates:</span></span>

* <span data-ttu-id="73e31-345">FK 필드에 고유 인덱스를 추가하거나</span><span class="sxs-lookup"><span data-stu-id="73e31-345">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="73e31-346">`CourseAssignment`와 유사한 기본 복합 키로 `Enrollment`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-346">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="73e31-347">자세한 내용은 [인덱스](/ef/core/modeling/indexes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-347">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="73e31-348">데이터베이스 컨텍스트 업데이트</span><span class="sxs-lookup"><span data-stu-id="73e31-348">Update the database context</span></span>

<span data-ttu-id="73e31-349">다음 코드로 *Data/SchoolContext.cs*를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-349">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="73e31-350">위의 코드는 새 엔터티를 추가하고 `CourseAssignment` 엔터티의 복합 PK를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-350">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="73e31-351">특성에 대한 흐름 API 대안</span><span class="sxs-lookup"><span data-stu-id="73e31-351">Fluent API alternative to attributes</span></span>

<span data-ttu-id="73e31-352">위의 코드에서 `OnModelCreating` 메서드는 *흐름 API*를 사용하여 EF Core 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-352">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="73e31-353">API는 종종 일련의 메서드 호출을 단일 명령문으로 함께 연결하여 사용되기 때문에 “흐름”이라고 부릅니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-353">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="73e31-354">[다음 코드](/ef/core/modeling/#use-fluent-api-to-configure-a-model)는 흐름 API의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-354">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="73e31-355">이 자습서에서 흐름 API는 특성으로 수행될 수 없는 데이터베이스 매핑을 위해서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-355">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="73e31-356">그러나 흐름 API는 특성으로 수행될 수 있는 대부분의 서식 지정, 유효성 검사 및 매핑 규칙을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-356">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="73e31-357">`MinimumLength`와 같은 일부 특성은 흐름 API를 통해 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-357">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="73e31-358">`MinimumLength`는 스키마를 변경하지 않으며 최소 길이 유효성 검사 규칙만 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-358">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="73e31-359">일부 개발자는 흐름 API를 단독으로 사용하는 것을 선호하므로 자신의 엔터티 클래스를 “정리”할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-359">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="73e31-360">특성 및 흐름 API를 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-360">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="73e31-361">흐름 API로만 수행될 수 있는 일부 구성이 있습니다(복합 PK 지정).</span><span class="sxs-lookup"><span data-stu-id="73e31-361">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="73e31-362">특성으로만 수행될 수 있는 일부 구성이 있습니다(`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="73e31-362">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="73e31-363">흐름 API 또는 특성을 사용하기 위한 권장 방법:</span><span class="sxs-lookup"><span data-stu-id="73e31-363">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="73e31-364">이 두 가지 방법 중 하나를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-364">Choose one of these two approaches.</span></span>
* <span data-ttu-id="73e31-365">가능한 한 계속 선택한 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-365">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="73e31-366">이 자습서에서 사용되는 일부 특성은 다음에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-366">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="73e31-367">유효성 검사에만(예: `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="73e31-367">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="73e31-368">EF Core 구성에만(예: `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="73e31-368">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="73e31-369">유효성 검사 및 EF Core 구성(예: `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="73e31-369">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="73e31-370">특성과 흐름 API의 비교에 대한 자세한 내용은 [구성 메서드](/ef/core/modeling/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-370">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="73e31-371">엔터티 다이어그램</span><span class="sxs-lookup"><span data-stu-id="73e31-371">Entity diagram</span></span>

<span data-ttu-id="73e31-372">다음 그림은 EF Power Tools가 완벽한 School 모델을 만드는 다이어그램을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-372">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="73e31-374">위의 다이어그램은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-374">The preceding diagram shows:</span></span>

* <span data-ttu-id="73e31-375">여러 일대다 관계 줄(1 ~ \*)</span><span class="sxs-lookup"><span data-stu-id="73e31-375">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="73e31-376">`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계 줄(1 ~ 0..1)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-376">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="73e31-377">`Instructor` 및 `Department` 엔터티 사이에는 영 또는 일대다 관계 줄(0..1 ~ \*)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-377">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="73e31-378">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="73e31-378">Seed the database</span></span>

<span data-ttu-id="73e31-379">*Data/DbInitializer.cs*에서 코드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-379">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="73e31-380">위의 코드는 새 엔터티에 대한 시드 데이터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-380">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="73e31-381">이 코드의 대부분은 새 엔터티 개체를 만들고 샘플 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-381">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="73e31-382">샘플 데이터는 테스트를 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-382">The sample data is used for testing.</span></span> <span data-ttu-id="73e31-383">다 대 다 조인 테이블을 시드할 수 있는 예제는 `Enrollments` 및 `CourseAssignments`를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-383">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="73e31-384">마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="73e31-384">Add a migration</span></span>

<span data-ttu-id="73e31-385">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-385">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-386">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73e31-387">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-387">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="73e31-388">위의 명령은 가능한 데이터 손실에 대한 경고를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-388">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="73e31-389">`database update` 명령이 실행되는 경우 다음과 같은 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-389">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="73e31-390">다음 섹션에서는 이 오류에 대해 수행할 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-390">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73e31-392">마이그레이션을 추가하고 `database update` 명령을 실행하는 경우 다음 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-392">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="73e31-393">다음 섹션에서는 이 오류를 방지하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-393">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="73e31-394">마이그레이션 적용 또는 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-394">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="73e31-395">기존 데이터베이스가 있으므로 변경 내용을 적용하는 방법을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-395">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="73e31-396">이 자습서에서는 다음 두 가지 대안을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-396">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="73e31-397">[데이터베이스를 삭제하고 다시 만들기](#drop).</span><span class="sxs-lookup"><span data-stu-id="73e31-397">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="73e31-398">SQLite를 사용하는 경우 이 섹션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-398">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="73e31-399">[기존 데이터베이스에 마이그레이션 적용](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="73e31-399">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="73e31-400">이 섹션의 지침은 SQL Server에만 적용되며 **SQLite에는 적용되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="73e31-400">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="73e31-401">두 방법 모두 SQL Server에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-401">Either choice works for SQL Server.</span></span> <span data-ttu-id="73e31-402">마이그레이션 적용 방법은 더 복잡하고 시간이 오래 걸리지만 실제 프로덕션 환경에 권장되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-402">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="73e31-403">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-403">Drop and re-create the database</span></span>

<span data-ttu-id="73e31-404">SQL Server를 사용하고 다음 섹션의 마이그레이션 적용 방법을 수행하려는 경우 [이 섹션을 건너뜁니다](#apply-the-migration).</span><span class="sxs-lookup"><span data-stu-id="73e31-404">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="73e31-405">EF Core가 새로운 데이터베이스를 만들도록 강제하려면 데이터베이스를 삭제하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-405">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73e31-407">PMC(**패키지 관리자 콘솔**)에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-407">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="73e31-408">*마이그레이션* 폴더를 삭제 한 후 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-408">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="73e31-410">명령 창을 열고 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-410">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="73e31-411">프로젝트 폴더에는 *ContosoUniversity.csproj* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-411">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="73e31-412">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-412">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="73e31-413">*마이그레이션* 폴더를 삭제 한 후 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-413">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="73e31-414">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-414">Run the app.</span></span> <span data-ttu-id="73e31-415">앱을 실행하면 `DbInitializer.Initialize` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-415">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="73e31-416">`DbInitializer.Initialize`는 새 데이터베이스를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-416">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-417">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-417">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73e31-418">SSOX에서 데이터베이스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-418">Open the database in SSOX:</span></span>

* <span data-ttu-id="73e31-419">SSOX가 이전에 열려 있던 경우 **새로 고침** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-419">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="73e31-420">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-420">Expand the **Tables** node.</span></span> <span data-ttu-id="73e31-421">생성된 테이블이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-421">The created tables are displayed.</span></span>

  ![SSOX의 테이블](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="73e31-423">**CourseAssignment** 테이블을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-423">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="73e31-424">**CourseAssignment** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-424">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="73e31-425">**CourseAssignment** 테이블에 데이터가 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-425">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73e31-428">SQLite 도구를 사용하여 데이터베이스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-428">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="73e31-429">새 테이블 및 열.</span><span class="sxs-lookup"><span data-stu-id="73e31-429">New tables and columns.</span></span>
* <span data-ttu-id="73e31-430">**CourseAssignment** 테이블과 같은 테이블의 시드된 데이터.</span><span class="sxs-lookup"><span data-stu-id="73e31-430">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="73e31-431">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="73e31-431">Apply the migration</span></span>

<span data-ttu-id="73e31-432">이 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-432">This section is optional.</span></span> <span data-ttu-id="73e31-433">이 단계는 SQL Server LocalDB에서만, 앞의 [데이터베이스를 삭제하고 다시 만들기](#drop) 섹션을 건너뛴 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-433">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="73e31-434">마이그레이션이 기존 데이터로 실행될 때 기존 데이터로 충족되지 않는 FK 제약 조건이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="73e31-435">프로덕션 데이터와 함께 기존 데이터를 마이그레이션하도록 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="73e31-436">이 섹션에서는 FK 제약 조건 위반을 수정하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="73e31-437">백업 없이 이러한 코드 변경을 만들지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="73e31-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="73e31-438">앞의 [데이터베이스를 삭제하고 다시 만들기](#drop) 섹션을 완료한 경우 이 코드를 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-438">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="73e31-439">*{timestamp}_ComplexDataModel.cs* 파일은 다음 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="73e31-440">위의 코드는 Null을 허용하지 않는 `DepartmentID` FK를 `Course` 테이블에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="73e31-441">테이블을 마이그레이션에서 업데이트할 수 없도록 이전 자습서의 데이터베이스는 `Course`에 행을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-441">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="73e31-442">기존 데이터를 사용하여 `ComplexDataModel` 마이그레이션 작업을 수행하려면:</span><span class="sxs-lookup"><span data-stu-id="73e31-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="73e31-443">새 열(`DepartmentID`)에 기본값을 제공하도록 코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="73e31-444">기본 부서로 작동하도록 "Temp"라는 가짜 부서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="73e31-445">외래 키 제약 조건 수정</span><span class="sxs-lookup"><span data-stu-id="73e31-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="73e31-446">`ComplexDataModel` 마이그레이션 클래스에서 `Up` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-446">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="73e31-447">*{timestamp}_ComplexDataModel.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="73e31-448">`DepartmentID` 열을 `Course` 테이블에 추가하는 코드 줄을 주석으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="73e31-449">다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-449">Add the following highlighted code.</span></span> <span data-ttu-id="73e31-450">새 코드는 `.CreateTable( name: "Department"` 블록 뒤로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="73e31-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span><span class="sxs-lookup"><span data-stu-id="73e31-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="73e31-452">앞의 변경 내용으로 `ComplexDataModel.Up` 메서드를 실행한 후에 기존 `Course` 행은 “Temp” 부서에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-452">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="73e31-453">여기에 표시된 상황을 처리하는 방법은 이 자습서에서 사용하도록 간소화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-453">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="73e31-454">프로덕션 앱은:</span><span class="sxs-lookup"><span data-stu-id="73e31-454">A production app would:</span></span>

* <span data-ttu-id="73e31-455">`Department` 행 및 관련 `Course` 행을 새 `Department` 행에 추가하는 코드 또는 스크립트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-455">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="73e31-456">`Course.DepartmentID`에 대해 "Temp" 부서 또는 기본값을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-456">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="73e31-458">PMC(**패키지 관리자 콘솔**)에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-458">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="73e31-459">`DbInitializer.Initialize` 메서드는 빈 데이터베이스에서만 작동하도록 디자인되었으므로 SSOX를 사용하여 Student 및 Course 테이블의 모든 행을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-459">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="73e31-460">(계단식 삭제는 Enrollment 테이블을 처리합니다.)</span><span class="sxs-lookup"><span data-stu-id="73e31-460">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-461">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-461">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="73e31-462">Visual Studio Code와 함께 SQL Server LocalDB를 사용하는 경우 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-462">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="73e31-463">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-463">Run the app.</span></span> <span data-ttu-id="73e31-464">앱을 실행하면 `DbInitializer.Initialize` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-464">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="73e31-465">`DbInitializer.Initialize`는 새 데이터베이스를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-465">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="73e31-466">다음 단계</span><span class="sxs-lookup"><span data-stu-id="73e31-466">Next steps</span></span>

<span data-ttu-id="73e31-467">다음 두 자습서에서는 관련 데이터를 읽고 업데이트하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-467">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="73e31-468">[이전 자습서](xref:data/ef-rp/migrations)
> [다음 자습서](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="73e31-468">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73e31-469">이전 자습서에서는 세 가지 엔터티로 구성된 기본 데이터 모델을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-469">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="73e31-470">이 자습서에서:</span><span class="sxs-lookup"><span data-stu-id="73e31-470">In this tutorial:</span></span>

* <span data-ttu-id="73e31-471">더 많은 엔터티 및 관계가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-471">More entities and relationships are added.</span></span>
* <span data-ttu-id="73e31-472">데이터 모델은 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하여 사용자 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-472">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="73e31-473">완성된 데이터 모델에 대한 엔터티 클래스는 다음 그림에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-473">The entity classes for the completed data model are shown in the following illustration:</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="73e31-475">해결할 수 없는 문제가 발생한 경우 [완성된 앱](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-475">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="73e31-476">특성을 사용하여 데이터 모델 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="73e31-476">Customize the data model with attributes</span></span>

<span data-ttu-id="73e31-477">이 섹션에서 데이터 모델은 특성을 사용하여 사용자 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-477">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="73e31-478">DataType 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-478">The DataType attribute</span></span>

<span data-ttu-id="73e31-479">학생 페이지는 현재 등록 날짜의 시간을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-479">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="73e31-480">일반적으로 날짜 필드는 시간이 아닌 날짜만을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-480">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="73e31-481">*Models/Student.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-481">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="73e31-482">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-482">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="73e31-483">이 경우 날짜 및 시간이 아닌 날짜만 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-483">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="73e31-484">[DataType 열거형](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)은 날짜, 시간, 전화 번호, 통화, 이메일 주소 등과 같은 많은 데이터 형식을 제공합니다. `DataType` 특성을 통해 앱에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-484">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="73e31-485">예:</span><span class="sxs-lookup"><span data-stu-id="73e31-485">For example:</span></span>

* <span data-ttu-id="73e31-486">`mailto:` 링크는 `DataType.EmailAddress`에 대해 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-486">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="73e31-487">날짜 선택기는 대부분의 브라우저에서 `DataType.Date`에 대해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-487">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="73e31-488">`DataType` 특성은 HTML 5 브라우저에서 사용하는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-488">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="73e31-489">`DataType` 특성은 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-489">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="73e31-490">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-490">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="73e31-491">기본적으로 날짜 필드는 서버의 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)를 기본으로 하는 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-491">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="73e31-492">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-492">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="73e31-493">`ApplyFormatInEditMode` 설정은 서식 지정이 편집 UI에도 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-493">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="73e31-494">일부 필드는 `ApplyFormatInEditMode`를 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-494">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="73e31-495">예를 들어 통화 기호는 일반적으로 편집 텍스트 상자에 표시되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-495">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="73e31-496">`DisplayFormat` 특성은 단독으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-496">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="73e31-497">일반적으로 `DisplayFormat` 특성과 함께 `DataType` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-497">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="73e31-498">`DataType` 특성은 화면에서 렌더링하는 방법과 대조적으로 데이터의 의미 체계를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-498">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="73e31-499">`DataType` 특성은 `DisplayFormat`에서 사용할 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-499">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="73e31-500">브라우저는 HTML5 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-500">The browser can enable HTML5 features.</span></span> <span data-ttu-id="73e31-501">예를 들어 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크, 클라이언트 쪽 입력 유효성 검사 등을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-501">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="73e31-502">기본적으로 브라우저는 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-502">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="73e31-503">자세한 내용은 [\<입력> 태그 도우미 설명서](xref:mvc/views/working-with-forms#the-input-tag-helper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-503">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="73e31-504">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-504">Run the app.</span></span> <span data-ttu-id="73e31-505">학생 인덱스 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-505">Navigate to the Students Index page.</span></span> <span data-ttu-id="73e31-506">시간이 더 이상 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-506">Times are no longer displayed.</span></span> <span data-ttu-id="73e31-507">`Student` 모델을 사용하는 모든 보기는 시간을 제외한 날짜를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-507">Every view that uses the `Student` model displays the date without time.</span></span>

![시간 없이 날짜를 표시하는 학생 인덱스 페이지](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="73e31-509">StringLength 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-509">The StringLength attribute</span></span>

<span data-ttu-id="73e31-510">데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지는 특성으로 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-510">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="73e31-511">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 특성은 데이터 필드에서 허용되는 최소 및 최대 문자 길이를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-511">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="73e31-512">`StringLength` 특성은 또한 클라이언트 쪽 및 서버 쪽 유효성 검사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-512">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="73e31-513">최소값은 데이터베이스 스키마에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-513">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="73e31-514">`Student` 모델을 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-514">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="73e31-515">위의 코드는 이름을 최대 50자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-515">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="73e31-516">`StringLength` 특성은 이름에 공백을 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-516">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="73e31-517">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 특성은 입력에 제한을 적용하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-517">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="73e31-518">예를 들어 다음 코드는 첫 번째 문자가 대문자여야 하고, 나머지 문자는 사전순이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-518">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="73e31-519">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-519">Run the app:</span></span>

* <span data-ttu-id="73e31-520">학생 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-520">Navigate to the Students page.</span></span>
* <span data-ttu-id="73e31-521">**새로 만들기**를 선택하고, 50자보다 긴 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-521">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="73e31-522">**만들기**를 선택하면 클라이언트 쪽 유효성 검사가 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-522">Select **Create**, client-side validation shows an error message.</span></span>

![문자열 길이 오류를 보여 주는 학생 인덱스 페이지](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="73e31-524">**SQL Server 개체 탐색기**(SSOX)에서 **학생** 테이블을 두 번 클릭하여 학생 테이블 디자이너를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-524">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![마이그레이션 전 SSOX의 학생 테이블](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="73e31-526">위의 이미지는 `Student` 테이블에 대한 스키마를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-526">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="73e31-527">마이그레이션은 DB에서 실행되지 않기 때문에 이름 필드에는 `nvarchar(MAX)` 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-527">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="73e31-528">마이그레이션이 이 자습서의 뒷부분에서 실행될 때 이름 필드는 `nvarchar(50)`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-528">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="73e31-529">열 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-529">The Column attribute</span></span>

<span data-ttu-id="73e31-530">특성은 클래스 및 속성이 데이터베이스에 매핑되는 방법을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-530">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="73e31-531">이 섹션에서 `Column` 특성은 DB에서 `FirstMidName` 속성의 이름을 "FirstName"으로 매핑하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-531">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="73e31-532">DB가 만들어질 때 모델의 속성 이름은 열 이름에 사용됩니다(`Column` 특성이 사용되는 경우 제외).</span><span class="sxs-lookup"><span data-stu-id="73e31-532">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="73e31-533">필드에 중간 이름도 포함될 수 있으므로 `Student` 모델은 첫 번째 이름 필드에 대해 `FirstMidName`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-533">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="73e31-534">*Student.cs* 파일을 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-534">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="73e31-535">이전 변경으로 인해 앱의 `Student.FirstMidName`은 `Student` 테이블의 `FirstName` 열로 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-535">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="73e31-536">`Column` 특성을 추가하면 `SchoolContext`를 지원하는 모델이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-536">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="73e31-537">`SchoolContext`를 지원하는 모델은 데이터베이스와 더 이상 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-537">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="73e31-538">마이그레이션을 적용하기 전에 앱이 실행되는 경우 다음과 같은 예외가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-538">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="73e31-539">DB를 업데이트하려면:</span><span class="sxs-lookup"><span data-stu-id="73e31-539">To update the DB:</span></span>

* <span data-ttu-id="73e31-540">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-540">Build the project.</span></span>
* <span data-ttu-id="73e31-541">프로젝트 폴더의 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-541">Open a command window in the project folder.</span></span> <span data-ttu-id="73e31-542">다음 명령을 입력하여 새 마이그레이션을 만들고 DB를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-542">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-543">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="73e31-545">`migrations add ColumnFirstName` 명령은 다음과 같은 경고 메시지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-545">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="73e31-546">이름 필드는 이제 50자로 제한되기 때문에 경고가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-546">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="73e31-547">DB의 이름에 50자 이상의 문자가 있는 경우 51자부터 마지막 문자까지가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-547">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="73e31-548">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-548">Test the app.</span></span>

<span data-ttu-id="73e31-549">SSOX에서 학생 테이블을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-549">Open the Student table in SSOX:</span></span>

![마이그레이션 후 SSOX의 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="73e31-551">마이그레이션이 적용되기 전에 이름 열은 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) 형식이었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-551">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="73e31-552">이름 열은 이제 `nvarchar(50)`입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-552">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="73e31-553">열 이름은 `FirstMidName`에서 `FirstName`으로 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-553">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="73e31-554">다음 섹션의 일부 단계에서 앱 빌드는 컴파일러 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-554">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="73e31-555">지침은 앱을 빌드하는 시기를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-555">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="73e31-556">학생 엔터티 업데이트</span><span class="sxs-lookup"><span data-stu-id="73e31-556">Student entity update</span></span>

![학생 엔터티](complex-data-model/_static/student-entity.png)

<span data-ttu-id="73e31-558">*Models/Student.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-558">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="73e31-559">필수 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-559">The Required attribute</span></span>

<span data-ttu-id="73e31-560">`Required` 특성에서 이름 속성은 필수 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-560">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="73e31-561">`Required` 특성은 값 형식(`DateTime`, `int`, `double` 등)과 같은 비 nullable 형식에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-561">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="73e31-562">Null일 수 없는 형식은 자동으로 필수 필드로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-562">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="73e31-563">`Required` 특성은 `StringLength` 특성에서 최소 길이 매개 변수로 대체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-563">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="73e31-564">표시 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-564">The Display attribute</span></span>

<span data-ttu-id="73e31-565">`Display` 특성은 텍스트 상자에 대한 캡션이 "First Name", "Last Name", "Full Name" 및 "Enrollment Date"여야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-565">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="73e31-566">기본 캡션에는 단어를 분할하는 공백이 없습니다(예: "Lastname)".</span><span class="sxs-lookup"><span data-stu-id="73e31-566">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="73e31-567">FullName 계산된 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-567">The FullName calculated property</span></span>

<span data-ttu-id="73e31-568">`FullName`은 다른 두 개의 속성을 연결하여 생성되는 값을 반환하는 계산된 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-568">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="73e31-569">`FullName`은 설정될 수 없습니다. get 접근자만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-569">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="73e31-570">데이터베이스에서 `FullName` 열이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-570">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="73e31-571">강사 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-571">Create the Instructor Entity</span></span>

![강사 엔터티](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="73e31-573">다음 코드로 *Models/Instructor.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-573">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="73e31-574">한 줄에 여러 특성이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-574">Multiple attributes can be on one line.</span></span> <span data-ttu-id="73e31-575">`HireDate` 특성은 다음과 같이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-575">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="73e31-576">CourseAssignments 및 OfficeAssignment 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-576">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="73e31-577">`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-577">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="73e31-578">강사는 여러 강좌를 가르칠 수 있으므로 `CourseAssignments`는 컬렉션으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-578">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="73e31-579">탐색 속성이 여러 엔터티를 보유하는 경우:</span><span class="sxs-lookup"><span data-stu-id="73e31-579">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="73e31-580">항목을 추가, 삭제 및 업데이트할 수 있는 목록 형식이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-580">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="73e31-581">탐색 속성 유형은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-581">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="73e31-582">`ICollection<T>`가 지정되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-582">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="73e31-583">`CourseAssignment` 엔터티는 다대다 관계의 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-583">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="73e31-584">Contoso University 비즈니스 규칙은 한 명의 강사가 최대 하나의 사무실을 가질 수 있음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-584">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="73e31-585">`OfficeAssignment` 속성은 단일 `OfficeAssignment` 엔터티를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-585">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="73e31-586">사무실이 할당되지 않은 경우 `OfficeAssignment`는 Null입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-586">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="73e31-587">OfficeAssignment 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-587">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="73e31-589">다음 코드로 *Models/OfficeAssignment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-589">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="73e31-590">키 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-590">The Key attribute</span></span>

<span data-ttu-id="73e31-591">`[Key]` 특성은 속성 이름이 classnameID 또는 ID가 아닌 다른 것일 때 PK(기본 키)로 속성을 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-591">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="73e31-592">`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-592">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="73e31-593">사무실 할당은 할당된 강사와 관련하여 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-593">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="73e31-594">`OfficeAssignment` PK는 `Instructor` 엔터티에 대한 해당 FK(외래 키)이기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-594">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="73e31-595">EF Core는 다음과 같은 이유로 `OfficeAssignment`의 PK로 `InstructorID`를 자동으로 인식할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-595">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="73e31-596">`InstructorID`는 ID 또는 classnameID 명명 규칙을 따르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-596">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="73e31-597">따라서 `Key` 특성은 PK로 `InstructorID`를 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-597">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="73e31-598">기본적으로 EF Core는 열이 관계 확인을 위한 것이기 때문에 키를 데이터베이스에서 생성되지 않은 것으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-598">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="73e31-599">강사 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-599">The Instructor navigation property</span></span>

<span data-ttu-id="73e31-600">`Instructor` 엔터티에 대한 `OfficeAssignment` 탐색 속성은 다음과 같은 이유로 nullable입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-600">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="73e31-601">참조 형식(예: 클래스는 nullable)</span><span class="sxs-lookup"><span data-stu-id="73e31-601">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="73e31-602">강사는 사무실 할당이 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-602">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="73e31-603">`OfficeAssignment` 엔터티는 다음과 같은 이유로 비 nullable `Instructor` 탐색 속성을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-603">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="73e31-604">`InstructorID`은 Null을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-604">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="73e31-605">사무실 할당은 강사 없이 존재할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-605">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="73e31-606">`Instructor` 엔터티에 관련된 `OfficeAssignment` 엔터티가 있는 경우 각 엔터티는 해당 탐색 속성의 다른 엔터티에 대한 참조를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-606">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="73e31-607">`[Required]` 특성은 `Instructor` 탐색 속성에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-607">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="73e31-608">위의 코드는 관련된 강사가 있어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-608">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="73e31-609">`InstructorID` 외래 키(PK이기도 함)는 Null을 허용하지 않으므로 위의 코드는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-609">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="73e31-610">강좌 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="73e31-610">Modify the Course Entity</span></span>

![강좌 엔터티](complex-data-model/_static/course-entity.png)

<span data-ttu-id="73e31-612">*Models/Course.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-612">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="73e31-613">`Course` 엔터티에는 FK(외래 키) 속성 `DepartmentID`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-613">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="73e31-614">`DepartmentID`는 관련된 `Department` 엔터티를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-614">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="73e31-615">`Course` 엔터티에는 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-615">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="73e31-616">모델에 관련된 엔터티에 대한 탐색 속성이 있는 경우 EF Core는 데이터 모델에 대한 FK 속성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-616">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="73e31-617">EF Core는 필요한 어디든지 데이터베이스에 FK를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-617">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="73e31-618">EF Core는 자동으로 만들어진 FK에 대한 [섀도 속성](/ef/core/modeling/shadow-properties)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-618">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="73e31-619">데이터 모델에 FK가 있으면 더 간단하고 더 효율적으로 업데이트를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-619">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="73e31-620">예를 들어 FK 키 속성 `DepartmentID`가 포함되지 *않은* 모델을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-620">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="73e31-621">과정 엔터티가 편집을 위해 페치되는 경우:</span><span class="sxs-lookup"><span data-stu-id="73e31-621">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="73e31-622">`Department` 엔터티는 명시적으로 로드되지 않은 경우 Null입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-622">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="73e31-623">과정 엔터티를 업데이트하려면 `Department` 엔터티를 먼저 페치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-623">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="73e31-624">FK 속성 `DepartmentID`가 데이터 모델에 포함된 경우 업데이트하기 전에 `Department` 엔터티를 페치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-624">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="73e31-625">DatabaseGenerated 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-625">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="73e31-626">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 특성은 PK가 데이터베이스에서 생성되지 않고 애플리케이션에서 제공되는 것을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-626">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="73e31-627">기본적으로 EF Core는 PK 값이 DB에서 생성되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-627">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="73e31-628">DB에서 생성된 PK 값은 일반적으로 가장 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-628">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="73e31-629">`Course` 엔터티의 경우 사용자는 PK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-629">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="73e31-630">예를 들어 수학 부서에 대한 1000 시리즈, 영어 부서에 대한 2000 시리즈와 같은 강좌 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-630">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="73e31-631">`DatabaseGenerated` 특성은 기본 값을 생성하는 데 사용될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-631">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="73e31-632">예를 들어 DB는 행이 만들어지거나 업데이트된 날짜를 기록하기 위해 날짜 필드를 자동으로 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-632">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="73e31-633">자세한 내용은 [생성된 속성](/ef/core/modeling/generated-properties)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-633">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="73e31-634">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-634">Foreign key and navigation properties</span></span>

<span data-ttu-id="73e31-635">`Course` 엔터티의 FK(외래 키) 속성 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-635">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="73e31-636">강좌는 하나의 부서에 할당되었으므로 `DepartmentID` FK 및 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-636">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="73e31-637">강좌에는 등록된 학생이 여러 명 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-637">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="73e31-638">여러 강사가 한 강좌를 수업할 수 있으므로 `CourseAssignments` 탐색 속성은 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-638">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="73e31-639">`CourseAssignment`는 [나중에](#many-to-many-relationships) 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-639">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="73e31-640">부서 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-640">Create the Department entity</span></span>

![부서 엔터티](complex-data-model/_static/department-entity.png)

<span data-ttu-id="73e31-642">다음 코드로 *Models/Department.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-642">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="73e31-643">열 특성</span><span class="sxs-lookup"><span data-stu-id="73e31-643">The Column attribute</span></span>

<span data-ttu-id="73e31-644">이전에 `Column` 특성은 열 이름 매핑을 변경하는 데 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-644">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="73e31-645">`Department` 엔터티에 대한 코드에서 `Column` 특성은 SQL 데이터 형식 매핑을 변경하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-645">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="73e31-646">`Budget` 열은 DB에서 SQL Server money 형식을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-646">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="73e31-647">열 매핑은 일반적으로 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-647">Column mapping is generally not required.</span></span> <span data-ttu-id="73e31-648">EF Core는 일반적으로 속성에 대한 CLR 형식에 따라 적절한 SQL Server 데이터 형식을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-648">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="73e31-649">CLR `decimal` 형식은 SQL Server `decimal` 유형에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-649">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="73e31-650">`Budget`은 통화에 대한 것이고 money 데이터 형식은 통화에 더욱 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-650">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="73e31-651">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-651">Foreign key and navigation properties</span></span>

<span data-ttu-id="73e31-652">FK 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-652">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="73e31-653">부서는 관리자를 갖거나 갖지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-653">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="73e31-654">관리자는 항상 강사입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-654">An administrator is always an instructor.</span></span> <span data-ttu-id="73e31-655">따라서 `InstructorID` 속성은 `Instructor` 엔터티에 FK로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-655">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="73e31-656">탐색 속성이 `Administrator`로 명명되나, `Instructor` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-656">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="73e31-657">위의 코드에서 물음표(?)는 속성이 nullable임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-657">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="73e31-658">부서에는 강좌가 많이 있을 수 있으므로 강좌 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-658">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="73e31-659">참고: 규칙에 따라 EF Core는 Null을 허용하지 않는 FK 및 다대다 관계에 대한 계단식 삭제를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-659">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="73e31-660">계단식 삭제로 인해 순환 계단식 삭제 규칙이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-660">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="73e31-661">순환 계단식 삭제 규칙은 마이그레이션이 추가될 때 예외를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-661">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="73e31-662">예를 들어 `Department.InstructorID` 속성이 null 허용 안 함으로 정의된 경우:</span><span class="sxs-lookup"><span data-stu-id="73e31-662">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="73e31-663">EF Core는 강사가 삭제될 때 부서를 삭제하도록 계단식 삭제 규칙을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-663">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="73e31-664">강사가 삭제될 때 부서 삭제는 의도된 동작이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-664">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="73e31-665">다음 흐름 API는 계단식 대신 제한 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-665">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="73e31-666">위의 코드는 부서 강사 관계에서 계단식 삭제를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-666">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="73e31-667">등록 엔터티 업데이트</span><span class="sxs-lookup"><span data-stu-id="73e31-667">Update the Enrollment entity</span></span>

<span data-ttu-id="73e31-668">등록 레코드는 한 명의 학생이 수행하는 하나의 강좌에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-668">An enrollment record is for one course taken by one student.</span></span>

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="73e31-670">*Models/Enrollment.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-670">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="73e31-671">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="73e31-671">Foreign key and navigation properties</span></span>

<span data-ttu-id="73e31-672">FK 속성 및 탐색 속성은 다음 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-672">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="73e31-673">등록 레코드는 하나의 강좌에 해당하므로 `CourseID` FK 속성 및 `Course` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-673">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="73e31-674">등록 레코드는 한 명의 학생에 해당하므로 `StudentID` FK 속성 및 `Student` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-674">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="73e31-675">다대다 관계</span><span class="sxs-lookup"><span data-stu-id="73e31-675">Many-to-Many Relationships</span></span>

<span data-ttu-id="73e31-676">`Student` 및 `Course` 엔터티 사이에 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-676">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="73e31-677">`Enrollment` 엔터티는 데이터베이스에서 *페이로드를 사용하여* 다대다 조인 테이블로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-677">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="73e31-678">"페이로드 사용"은 `Enrollment` 테이블이 조인된 테이블에 대한 FK 외에도 추가 데이터를 포함하는 것을 의미합니다(이 경우 PK 및 `Grade`).</span><span class="sxs-lookup"><span data-stu-id="73e31-678">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="73e31-679">다음 그림은 이러한 관계 모양을 엔터티 다이어그램으로 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-679">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="73e31-680">(이 다이어그램은 EF 6.x에 대한 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)를 사용하여 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-680">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="73e31-681">다이어그램 만들기는 자습서의 일부가 아닙니다.)</span><span class="sxs-lookup"><span data-stu-id="73e31-681">Creating the diagram isn't part of the tutorial.)</span></span>

![학생-강좌 다대다 관계](complex-data-model/_static/student-course.png)

<span data-ttu-id="73e31-683">각 관계 줄에는 한쪽 끝에 1, 다른 한쪽 끝에는 별표(\*)가 있으며, 이는 일대다 관계를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-683">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="73e31-684">`Enrollment` 테이블에 등급 정보가 포함되지 않은 경우 두 개의 FK(`CourseID` 및 `StudentID`)를 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-684">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="73e31-685">페이로드가 없는 다대다 조인 테이블은 PJT(순수 조인 테이블)라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-685">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="73e31-686">`Instructor` 및 `Course` 엔터티에는 순수 조인 테이블을 사용하는 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-686">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="73e31-687">참고: EF 6.x는 다대다 관계에 대한 암시적 조인 테이블을 지원하지만 EF Core는 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-687">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="73e31-688">자세한 내용은 [EF Core 2.0에서 다대다 관계](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-688">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="73e31-689">CourseAssignment 엔터티</span><span class="sxs-lookup"><span data-stu-id="73e31-689">The CourseAssignment entity</span></span>

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="73e31-691">다음 코드로 *Models/CourseAssignment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-691">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="73e31-692">강사-강좌</span><span class="sxs-lookup"><span data-stu-id="73e31-692">Instructor-to-Courses</span></span>

![강사-강좌 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="73e31-694">강사-강좌 다대다 관계:</span><span class="sxs-lookup"><span data-stu-id="73e31-694">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="73e31-695">엔터티 집합으로 표현되어야 하는 조인 테이블이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-695">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="73e31-696">순수 조인 테이블(페이로드 없는 테이블)입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-696">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="73e31-697">조인 엔터티 `EntityName1EntityName2`의 이름을 지정하는 데 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-697">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="73e31-698">예를 들어 이 패턴을 사용하는 강사-강좌 조인 테이블은 `CourseInstructor`입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-698">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="73e31-699">그러나 관계를 설명하는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-699">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="73e31-700">데이터 모델은 단순하게 시작하고 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-700">Data models start out simple and grow.</span></span> <span data-ttu-id="73e31-701">페이로드 없는 조인(PJT)은 페이로드를 포함하도록 자주 발전합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-701">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="73e31-702">설명이 포함된 엔터티 이름으로 시작하여 이름은 조인 테이블이 변경될 때 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-702">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="73e31-703">이상적으로 조인 엔터티는 비즈니스 도메인에서 고유의 자연스러운(가능한 한 단어) 이름을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-703">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="73e31-704">예를 들어 Books 및 Customers는 Ratings라는 조인 엔터티를 통해 연결될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-704">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="73e31-705">강사-강좌 다대다 관계의 경우 `CourseAssignment`는 `CourseInstructor`보다 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-705">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="73e31-706">복합 키</span><span class="sxs-lookup"><span data-stu-id="73e31-706">Composite key</span></span>

<span data-ttu-id="73e31-707">FK는 Null을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-707">FKs are not nullable.</span></span> <span data-ttu-id="73e31-708">`CourseAssignment`에서 두 개의 FK(`InstructorID` 및 `CourseID`)는 함께 `CourseAssignment` 테이블의 각 행을 고유하게 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-708">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="73e31-709">`CourseAssignment`는 전용 PK가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-709">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="73e31-710">`InstructorID` 및 `CourseID` 속성은 복합 PK로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-710">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="73e31-711">복합 PK를 EF Core로 지정하는 유일한 방법은 *흐름 API*를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-711">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="73e31-712">다음 섹션에서는 복합 PK를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-712">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="73e31-713">복합 키는 다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-713">The composite key ensures:</span></span>

* <span data-ttu-id="73e31-714">하나의 강좌에 대해 여러 행이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-714">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="73e31-715">한 명의 강사에 대해 여러 행이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-715">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="73e31-716">동일한 강사 및 강좌에 대한 여러 행은 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-716">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="73e31-717">`Enrollment` 조인 엔터티는 고유한 PK를 정의하므로 이러한 종류의 중복이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-717">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="73e31-718">이러한 중복 항목을 방지하려면:</span><span class="sxs-lookup"><span data-stu-id="73e31-718">To prevent such duplicates:</span></span>

* <span data-ttu-id="73e31-719">FK 필드에 고유 인덱스를 추가하거나</span><span class="sxs-lookup"><span data-stu-id="73e31-719">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="73e31-720">`CourseAssignment`와 유사한 기본 복합 키로 `Enrollment`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-720">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="73e31-721">자세한 내용은 [인덱스](/ef/core/modeling/indexes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-721">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="73e31-722">DB 컨텍스트 업데이트</span><span class="sxs-lookup"><span data-stu-id="73e31-722">Update the DB context</span></span>

<span data-ttu-id="73e31-723">다음 강조 표시된 코드를 *Data/SchoolContext.cs*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-723">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="73e31-724">위의 코드는 새 엔터티를 추가하고 `CourseAssignment` 엔터티의 복합 PK를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-724">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="73e31-725">특성에 대한 흐름 API 대안</span><span class="sxs-lookup"><span data-stu-id="73e31-725">Fluent API alternative to attributes</span></span>

<span data-ttu-id="73e31-726">위의 코드에서 `OnModelCreating` 메서드는 *흐름 API*를 사용하여 EF Core 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-726">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="73e31-727">API는 종종 일련의 메서드 호출을 단일 명령문으로 함께 연결하여 사용되기 때문에 “흐름”이라고 부릅니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-727">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="73e31-728">[다음 코드](/ef/core/modeling/#use-fluent-api-to-configure-a-model)는 흐름 API의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-728">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="73e31-729">이 자습서에서 흐름 API는 특성으로 수행될 수 없는 DB 매핑을 위해서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-729">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="73e31-730">그러나 흐름 API는 특성으로 수행될 수 있는 대부분의 서식 지정, 유효성 검사 및 매핑 규칙을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-730">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="73e31-731">`MinimumLength`와 같은 일부 특성은 흐름 API를 통해 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-731">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="73e31-732">`MinimumLength`는 스키마를 변경하지 않으며 최소 길이 유효성 검사 규칙만 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-732">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="73e31-733">일부 개발자는 흐름 API를 단독으로 사용하는 것을 선호하므로 자신의 엔터티 클래스를 “정리”할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-733">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="73e31-734">특성 및 흐름 API를 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-734">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="73e31-735">흐름 API로만 수행될 수 있는 일부 구성이 있습니다(복합 PK 지정).</span><span class="sxs-lookup"><span data-stu-id="73e31-735">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="73e31-736">특성으로만 수행될 수 있는 일부 구성이 있습니다(`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="73e31-736">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="73e31-737">흐름 API 또는 특성을 사용하기 위한 권장 방법:</span><span class="sxs-lookup"><span data-stu-id="73e31-737">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="73e31-738">이 두 가지 방법 중 하나를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-738">Choose one of these two approaches.</span></span>
* <span data-ttu-id="73e31-739">가능한 한 계속 선택한 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-739">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="73e31-740">이 자습서에서 사용되는 일부 특성은 다음에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-740">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="73e31-741">유효성 검사에만(예: `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="73e31-741">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="73e31-742">EF Core 구성에만(예: `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="73e31-742">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="73e31-743">유효성 검사 및 EF Core 구성(예: `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="73e31-743">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="73e31-744">특성과 흐름 API의 비교에 대한 자세한 내용은 [구성 메서드](/ef/core/modeling/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-744">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="73e31-745">관계를 보여 주는 엔터티 다이어그램</span><span class="sxs-lookup"><span data-stu-id="73e31-745">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="73e31-746">다음 그림은 EF Power Tools가 완벽한 School 모델을 만드는 다이어그램을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-746">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="73e31-748">위의 다이어그램은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-748">The preceding diagram shows:</span></span>

* <span data-ttu-id="73e31-749">여러 일대다 관계 줄(1 ~ \*)</span><span class="sxs-lookup"><span data-stu-id="73e31-749">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="73e31-750">`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계 줄(1 ~ 0..1)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-750">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="73e31-751">`Instructor` 및 `Department` 엔터티 사이에는 영 또는 일대다 관계 줄(0..1 ~ \*)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-751">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="73e31-752">테스트 데이터로 DB 시드</span><span class="sxs-lookup"><span data-stu-id="73e31-752">Seed the DB with Test Data</span></span>

<span data-ttu-id="73e31-753">*Data/DbInitializer.cs*에서 코드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-753">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="73e31-754">위의 코드는 새 엔터티에 대한 시드 데이터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-754">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="73e31-755">이 코드의 대부분은 새 엔터티 개체를 만들고 샘플 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-755">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="73e31-756">샘플 데이터는 테스트를 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-756">The sample data is used for testing.</span></span> <span data-ttu-id="73e31-757">다 대 다 조인 테이블을 시드할 수 있는 예제는 `Enrollments` 및 `CourseAssignments`를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-757">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="73e31-758">마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="73e31-758">Add a migration</span></span>

<span data-ttu-id="73e31-759">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-759">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-760">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-760">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-761">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-761">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="73e31-762">위의 명령은 가능한 데이터 손실에 대한 경고를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-762">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="73e31-763">`database update` 명령이 실행되는 경우 다음과 같은 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-763">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="73e31-764">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="73e31-764">Apply the migration</span></span>

<span data-ttu-id="73e31-765">기존 데이터베이스가 있으므로 향후 변경 내용을 적용하는 방법을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-765">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="73e31-766">이 자습서에서는 두 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-766">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="73e31-767">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-767">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="73e31-768">[기존 데이터베이스에 마이그레이션 적용](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="73e31-768">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="73e31-769">이 방법은 더 복잡하고 시간이 오래 걸리지만 실제 프로덕션 환경에 권장되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-769">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="73e31-770">**참고**: 이는 자습서의 선택적 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-770">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="73e31-771">삭제하고 다시 만들기 단계를 수행하고 이 섹션을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-771">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="73e31-772">이 섹션의 단계를 수행하지 않으려면 삭제하고 다시 만들기 단계를 수행하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="73e31-772">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="73e31-773">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="73e31-773">Drop and re-create the database</span></span>

<span data-ttu-id="73e31-774">업데이트된 `DbInitializer`의 코드는 새 엔터티에 대한 시드 데이터를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-774">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="73e31-775">EF Core가 새로운 DB를 만들도록 강제하려면 DB를 삭제하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-775">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73e31-776">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73e31-776">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73e31-777">PMC(**패키지 관리자 콘솔**)에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-777">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="73e31-778">PMC에서 `Get-Help about_EntityFrameworkCore`를 실행하여 도움말 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-778">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73e31-779">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73e31-779">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73e31-780">명령 창을 열고 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-780">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="73e31-781">프로젝트 폴더에는 *Startup.cs* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-781">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="73e31-782">명령 창에서 다음을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-782">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="73e31-783">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-783">Run the app.</span></span> <span data-ttu-id="73e31-784">앱을 실행하면 `DbInitializer.Initialize` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-784">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="73e31-785">`DbInitializer.Initialize`는 새 DB를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-785">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="73e31-786">SSOX에서 DB를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-786">Open the DB in SSOX:</span></span>

* <span data-ttu-id="73e31-787">SSOX가 이전에 열려 있던 경우 **새로 고침** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-787">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="73e31-788">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-788">Expand the **Tables** node.</span></span> <span data-ttu-id="73e31-789">생성된 테이블이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-789">The created tables are displayed.</span></span>

![SSOX의 테이블](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="73e31-791">**CourseAssignment** 테이블을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-791">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="73e31-792">**CourseAssignment** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-792">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="73e31-793">**CourseAssignment** 테이블에 데이터가 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-793">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="73e31-795">기존 데이터베이스에 마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="73e31-795">Apply the migration to the existing database</span></span>

<span data-ttu-id="73e31-796">이 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-796">This section is optional.</span></span> <span data-ttu-id="73e31-797">이러한 단계는 이전의 [데이터베이스를 삭제하고 다시 만들기](#drop) 섹션을 건너뛴 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-797">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="73e31-798">마이그레이션이 기존 데이터로 실행될 때 기존 데이터로 충족되지 않는 FK 제약 조건이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-798">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="73e31-799">프로덕션 데이터와 함께 기존 데이터를 마이그레이션하도록 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-799">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="73e31-800">이 섹션에서는 FK 제약 조건 위반을 수정하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-800">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="73e31-801">백업 없이 이러한 코드 변경을 만들지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="73e31-801">Don't make these code changes without a backup.</span></span> <span data-ttu-id="73e31-802">이전 섹션을 완료하고 데이터베이스를 업데이트한 경우 이러한 코드 변경을 만들지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="73e31-802">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="73e31-803">*{timestamp}_ComplexDataModel.cs* 파일은 다음 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-803">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="73e31-804">위의 코드는 Null을 허용하지 않는 `DepartmentID` FK를 `Course` 테이블에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-804">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="73e31-805">테이블을 마이그레이션에서 업데이트할 수 없도록 이전 자습서의 DB는 `Course`에 행을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-805">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="73e31-806">기존 데이터를 사용하여 `ComplexDataModel` 마이그레이션 작업을 수행하려면:</span><span class="sxs-lookup"><span data-stu-id="73e31-806">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="73e31-807">새 열(`DepartmentID`)에 기본값을 제공하도록 코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-807">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="73e31-808">기본 부서로 작동하도록 "Temp"라는 가짜 부서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-808">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="73e31-809">외래 키 제약 조건 수정</span><span class="sxs-lookup"><span data-stu-id="73e31-809">Fix the foreign key constraints</span></span>

<span data-ttu-id="73e31-810">`ComplexDataModel` 클래스 `Up` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-810">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="73e31-811">*{timestamp}_ComplexDataModel.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-811">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="73e31-812">`DepartmentID` 열을 `Course` 테이블에 추가하는 코드 줄을 주석으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-812">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="73e31-813">다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-813">Add the following highlighted code.</span></span> <span data-ttu-id="73e31-814">새 코드는 `.CreateTable( name: "Department"` 블록 뒤로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-814">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="73e31-815">위의 변경 내용으로 `ComplexDataModel` `Up` 메서드를 실행한 후에 기존 `Course` 행은 “Temp” 부서에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-815">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="73e31-816">프로덕션 앱은:</span><span class="sxs-lookup"><span data-stu-id="73e31-816">A production app would:</span></span>

* <span data-ttu-id="73e31-817">`Department` 행 및 관련 `Course` 행을 새 `Department` 행에 추가하는 코드 또는 스크립트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-817">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="73e31-818">`Course.DepartmentID`에 대해 "Temp" 부서 또는 기본값을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-818">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="73e31-819">다음 자습서에서는 관련된 데이터를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="73e31-819">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73e31-820">추가 자료</span><span class="sxs-lookup"><span data-stu-id="73e31-820">Additional resources</span></span>

* [<span data-ttu-id="73e31-821">이 자습서의 YouTube 버전(1부)</span><span class="sxs-lookup"><span data-stu-id="73e31-821">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="73e31-822">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="73e31-822">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> <span data-ttu-id="73e31-823">[이전](xref:data/ef-rp/migrations)
> [다음](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="73e31-823">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end