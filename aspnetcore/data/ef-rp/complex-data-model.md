---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 데이터 모델 - 5/8
author: rick-anderson
description: 이 자습서에서는 더 많은 엔터티 및 관계를 추가하고, 서식 지정, 유효성 검사 및 매핑 규칙을 지정하여 데이터 모델을 사용자 지정합니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: f995f3f74da4910de518af875eb89349a8457573
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813707"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="b0a5f-103">ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 데이터 모델 - 5/8</span><span class="sxs-lookup"><span data-stu-id="b0a5f-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="b0a5f-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="b0a5f-105">이전 자습서에서는 세 가지 엔터티로 구성된 기본 데이터 모델을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="b0a5f-106">이 자습서에서:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-106">In this tutorial:</span></span>

* <span data-ttu-id="b0a5f-107">더 많은 엔터티 및 관계가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="b0a5f-108">데이터 모델은 서식 지정, 유효성 검사 및 데이터베이스 매핑 규칙을 지정하여 사용자 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="b0a5f-109">완성된 데이터 모델에 대한 엔터티 클래스는 다음 그림에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-109">The entity classes for the completed data model is shown in the following illustration:</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="b0a5f-111">해결할 수 없는 문제가 발생한 경우 [완성된 앱](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-111">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="b0a5f-112">특성을 사용하여 데이터 모델 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="b0a5f-112">Customize the data model with attributes</span></span>

<span data-ttu-id="b0a5f-113">이 섹션에서 데이터 모델은 특성을 사용하여 사용자 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-113">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="b0a5f-114">DataType 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-114">The DataType attribute</span></span>

<span data-ttu-id="b0a5f-115">학생 페이지는 현재 등록 날짜의 시간을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-115">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="b0a5f-116">일반적으로 날짜 필드는 시간이 아닌 날짜만을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-116">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="b0a5f-117">*Models/Student.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-117">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="b0a5f-118">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-118">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="b0a5f-119">이 경우 날짜 및 시간이 아닌 날짜만 표시되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-119">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="b0a5f-120">[DataType 열거형](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)은 날짜, 시간, 전화 번호, 통화, 이메일 주소 등과 같은 많은 데이터 형식을 제공합니다. `DataType` 특성을 통해 앱에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-120">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="b0a5f-121">예:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-121">For example:</span></span>

* <span data-ttu-id="b0a5f-122">`mailto:` 링크는 `DataType.EmailAddress`에 대해 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-122">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="b0a5f-123">날짜 선택기는 대부분의 브라우저에서 `DataType.Date`에 대해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-123">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="b0a5f-124">`DataType` 특성은 HTML 5 브라우저에서 사용하는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-124">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="b0a5f-125">`DataType` 특성은 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-125">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="b0a5f-126">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-126">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="b0a5f-127">기본적으로 날짜 필드는 서버의 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)를 기본으로 하는 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-127">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="b0a5f-128">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-128">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="b0a5f-129">`ApplyFormatInEditMode` 설정은 서식 지정이 편집 UI에도 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-129">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="b0a5f-130">일부 필드는 `ApplyFormatInEditMode`를 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-130">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="b0a5f-131">예를 들어 통화 기호는 일반적으로 편집 텍스트 상자에 표시되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-131">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="b0a5f-132">`DisplayFormat` 특성은 단독으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-132">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="b0a5f-133">일반적으로 `DisplayFormat` 특성과 함께 `DataType` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-133">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="b0a5f-134">`DataType` 특성은 화면에서 렌더링하는 방법과 대조적으로 데이터의 의미 체계를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="b0a5f-135">`DataType` 특성은 `DisplayFormat`에서 사용할 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-135">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="b0a5f-136">브라우저는 HTML5 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-136">The browser can enable HTML5 features.</span></span> <span data-ttu-id="b0a5f-137">예를 들어 달력 컨트롤, 로캘에 적합한 통화 기호, 이메일 링크, 클라이언트 쪽 입력 유효성 검사 등을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-137">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="b0a5f-138">기본적으로 브라우저는 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-138">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="b0a5f-139">자세한 내용은 [\<입력> 태그 도우미 설명서](xref:mvc/views/working-with-forms#the-input-tag-helper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-139">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="b0a5f-140">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-140">Run the app.</span></span> <span data-ttu-id="b0a5f-141">학생 인덱스 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-141">Navigate to the Students Index page.</span></span> <span data-ttu-id="b0a5f-142">시간이 더 이상 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-142">Times are no longer displayed.</span></span> <span data-ttu-id="b0a5f-143">`Student` 모델을 사용하는 모든 보기는 시간을 제외한 날짜를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-143">Every view that uses the `Student` model displays the date without time.</span></span>

![시간 없이 날짜를 표시하는 학생 인덱스 페이지](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="b0a5f-145">StringLength 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-145">The StringLength attribute</span></span>

<span data-ttu-id="b0a5f-146">데이터 유효성 검사 규칙 및 유효성 검사 오류 메시지는 특성으로 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="b0a5f-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 특성은 데이터 필드에서 허용되는 최소 및 최대 문자 길이를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="b0a5f-148">`StringLength` 특성은 또한 클라이언트 쪽 및 서버 쪽 유효성 검사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-148">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="b0a5f-149">최소값은 데이터베이스 스키마에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-149">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="b0a5f-150">`Student` 모델을 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-150">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="b0a5f-151">위의 코드는 이름을 최대 50자로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-151">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="b0a5f-152">`StringLength` 특성은 이름에 공백을 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="b0a5f-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 특성은 입력에 제한을 적용하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="b0a5f-154">예를 들어 다음 코드는 첫 번째 문자가 대문자여야 하고, 나머지 문자는 사전순이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="b0a5f-155">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-155">Run the app:</span></span>

* <span data-ttu-id="b0a5f-156">학생 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-156">Navigate to the Students page.</span></span>
* <span data-ttu-id="b0a5f-157">**새로 만들기**를 선택하고, 50자보다 긴 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-157">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="b0a5f-158">**만들기**를 선택하면 클라이언트 쪽 유효성 검사가 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-158">Select **Create**, client-side validation shows an error message.</span></span>

![문자열 길이 오류를 보여 주는 학생 인덱스 페이지](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="b0a5f-160">**SQL Server 개체 탐색기**(SSOX)에서 **학생** 테이블을 두 번 클릭하여 학생 테이블 디자이너를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-160">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![마이그레이션 전 SSOX의 학생 테이블](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="b0a5f-162">위의 이미지는 `Student` 테이블에 대한 스키마를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-162">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="b0a5f-163">마이그레이션은 DB에서 실행되지 않기 때문에 이름 필드에는 `nvarchar(MAX)` 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-163">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="b0a5f-164">마이그레이션이 이 자습서의 뒷부분에서 실행될 때 이름 필드는 `nvarchar(50)`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-164">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="b0a5f-165">열 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-165">The Column attribute</span></span>

<span data-ttu-id="b0a5f-166">특성은 클래스 및 속성이 데이터베이스에 매핑되는 방법을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-166">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="b0a5f-167">이 섹션에서 `Column` 특성은 DB에서 `FirstMidName` 속성의 이름을 "FirstName"으로 매핑하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-167">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="b0a5f-168">DB가 만들어질 때 모델의 속성 이름은 열 이름에 사용됩니다(`Column` 특성이 사용되는 경우 제외).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-168">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="b0a5f-169">필드에 중간 이름도 포함될 수 있으므로 `Student` 모델은 첫 번째 이름 필드에 대해 `FirstMidName`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-169">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="b0a5f-170">*Student.cs* 파일을 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-170">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="b0a5f-171">이전 변경으로 인해 앱의 `Student.FirstMidName`은 `Student` 테이블의 `FirstName` 열로 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-171">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="b0a5f-172">`Column` 특성을 추가하면 `SchoolContext`를 지원하는 모델이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="b0a5f-173">`SchoolContext`를 지원하는 모델은 데이터베이스와 더 이상 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="b0a5f-174">마이그레이션을 적용하기 전에 앱이 실행되는 경우 다음과 같은 예외가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-174">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="b0a5f-175">DB를 업데이트하려면:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-175">To update the DB:</span></span>

* <span data-ttu-id="b0a5f-176">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-176">Build the project.</span></span>
* <span data-ttu-id="b0a5f-177">프로젝트 폴더의 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-177">Open a command window in the project folder.</span></span> <span data-ttu-id="b0a5f-178">다음 명령을 입력하여 새 마이그레이션을 만들고 DB를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-178">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b0a5f-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0a5f-179">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b0a5f-180">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b0a5f-180">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="b0a5f-181">`migrations add ColumnFirstName` 명령은 다음과 같은 경고 메시지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-181">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="b0a5f-182">이름 필드는 이제 50자로 제한되기 때문에 경고가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-182">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="b0a5f-183">DB의 이름에 50자 이상의 문자가 있는 경우 51자부터 마지막 문자까지가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-183">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="b0a5f-184">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-184">Test the app.</span></span>

<span data-ttu-id="b0a5f-185">SSOX에서 학생 테이블을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-185">Open the Student table in SSOX:</span></span>

![마이그레이션 후 SSOX의 학생 테이블](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="b0a5f-187">마이그레이션이 적용되기 전에 이름 열은 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) 형식이었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-187">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="b0a5f-188">이름 열은 이제 `nvarchar(50)`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-188">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="b0a5f-189">열 이름은 `FirstMidName`에서 `FirstName`으로 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-189">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="b0a5f-190">다음 섹션의 일부 단계에서 앱 빌드는 컴파일러 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-190">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="b0a5f-191">지침은 앱을 빌드하는 시기를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-191">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="b0a5f-192">학생 엔터티 업데이트</span><span class="sxs-lookup"><span data-stu-id="b0a5f-192">Student entity update</span></span>

![학생 엔터티](complex-data-model/_static/student-entity.png)

<span data-ttu-id="b0a5f-194">*Models/Student.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-194">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="b0a5f-195">필수 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-195">The Required attribute</span></span>

<span data-ttu-id="b0a5f-196">`Required` 특성에서 이름 속성은 필수 필드입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-196">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="b0a5f-197">`Required` 특성은 값 형식(`DateTime`, `int`, `double` 등)과 같은 비 nullable 형식에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-197">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="b0a5f-198">Null일 수 없는 형식은 자동으로 필수 필드로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-198">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="b0a5f-199">`Required` 특성은 `StringLength` 특성에서 최소 길이 매개 변수로 대체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-199">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="b0a5f-200">표시 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-200">The Display attribute</span></span>

<span data-ttu-id="b0a5f-201">`Display` 특성은 텍스트 상자에 대한 캡션이 "First Name", "Last Name", "Full Name" 및 "Enrollment Date"여야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-201">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="b0a5f-202">기본 캡션에는 단어를 분할하는 공백이 없습니다(예: "Lastname)".</span><span class="sxs-lookup"><span data-stu-id="b0a5f-202">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="b0a5f-203">FullName 계산된 속성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-203">The FullName calculated property</span></span>

<span data-ttu-id="b0a5f-204">`FullName`은 다른 두 개의 속성을 연결하여 생성되는 값을 반환하는 계산된 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-204">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="b0a5f-205">`FullName`은 설정될 수 없습니다. get 접근자만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-205">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="b0a5f-206">데이터베이스에서 `FullName` 열이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-206">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="b0a5f-207">강사 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="b0a5f-207">Create the Instructor Entity</span></span>

![강사 엔터티](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="b0a5f-209">다음 코드로 *Models/Instructor.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-209">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="b0a5f-210">한 줄에 여러 특성이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-210">Multiple attributes can be on one line.</span></span> <span data-ttu-id="b0a5f-211">`HireDate` 특성은 다음과 같이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-211">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="b0a5f-212">CourseAssignments 및 OfficeAssignment 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-212">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="b0a5f-213">`CourseAssignments` 및 `OfficeAssignment` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-213">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="b0a5f-214">강사는 여러 강좌를 가르칠 수 있으므로 `CourseAssignments`는 컬렉션으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-214">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="b0a5f-215">탐색 속성이 여러 엔터티를 보유하는 경우:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-215">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="b0a5f-216">항목을 추가, 삭제 및 업데이트할 수 있는 목록 형식이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-216">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="b0a5f-217">탐색 속성 유형은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-217">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="b0a5f-218">`ICollection<T>`가 지정되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-218">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="b0a5f-219">`CourseAssignment` 엔터티는 다대다 관계의 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-219">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="b0a5f-220">Contoso University 비즈니스 규칙은 한 명의 강사가 최대 하나의 사무실을 가질 수 있음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-220">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="b0a5f-221">`OfficeAssignment` 속성은 단일 `OfficeAssignment` 엔터티를 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-221">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="b0a5f-222">사무실이 할당되지 않은 경우 `OfficeAssignment`는 Null입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-222">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="b0a5f-223">OfficeAssignment 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="b0a5f-223">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 엔터티](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="b0a5f-225">다음 코드로 *Models/OfficeAssignment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-225">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="b0a5f-226">키 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-226">The Key attribute</span></span>

<span data-ttu-id="b0a5f-227">`[Key]` 특성은 속성 이름이 classnameID 또는 ID가 아닌 다른 것일 때 PK(기본 키)로 속성을 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-227">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="b0a5f-228">`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-228">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="b0a5f-229">사무실 할당은 할당된 강사와 관련하여 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-229">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="b0a5f-230">`OfficeAssignment` PK는 `Instructor` 엔터티에 대한 해당 FK(외래 키)이기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-230">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="b0a5f-231">EF Core는 다음과 같은 이유로 `OfficeAssignment`의 PK로 `InstructorID`를 자동으로 인식할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-231">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="b0a5f-232">`InstructorID`는 ID 또는 classnameID 명명 규칙을 따르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-232">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="b0a5f-233">따라서 `Key` 특성은 PK로 `InstructorID`를 식별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="b0a5f-234">기본적으로 EF Core는 열이 관계 확인을 위한 것이기 때문에 키를 데이터베이스에서 생성되지 않은 것으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="b0a5f-235">강사 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-235">The Instructor navigation property</span></span>

<span data-ttu-id="b0a5f-236">`Instructor` 엔터티에 대한 `OfficeAssignment` 탐색 속성은 다음과 같은 이유로 nullable입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-236">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="b0a5f-237">참조 형식(예: 클래스는 nullable)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-237">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="b0a5f-238">강사는 사무실 할당이 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-238">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="b0a5f-239">`OfficeAssignment` 엔터티는 다음과 같은 이유로 비 nullable `Instructor` 탐색 속성을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-239">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="b0a5f-240">`InstructorID`은 Null을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-240">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="b0a5f-241">사무실 할당은 강사 없이 존재할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="b0a5f-242">`Instructor` 엔터티에 관련된 `OfficeAssignment` 엔터티가 있는 경우 각 엔터티는 해당 탐색 속성의 다른 엔터티에 대한 참조를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="b0a5f-243">`[Required]` 특성은 `Instructor` 탐색 속성에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-243">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="b0a5f-244">위의 코드는 관련된 강사가 있어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-244">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="b0a5f-245">`InstructorID` 외래 키(PK이기도 함)는 Null을 허용하지 않으므로 위의 코드는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-245">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="b0a5f-246">강좌 엔터티 수정</span><span class="sxs-lookup"><span data-stu-id="b0a5f-246">Modify the Course Entity</span></span>

![강좌 엔터티](complex-data-model/_static/course-entity.png)

<span data-ttu-id="b0a5f-248">*Models/Course.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-248">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="b0a5f-249">`Course` 엔터티에는 FK(외래 키) 속성 `DepartmentID`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-249">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="b0a5f-250">`DepartmentID`는 관련된 `Department` 엔터티를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-250">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="b0a5f-251">`Course` 엔터티에는 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-251">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="b0a5f-252">모델에 관련된 엔터티에 대한 탐색 속성이 있는 경우 EF Core는 데이터 모델에 대한 FK 속성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-252">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="b0a5f-253">EF Core는 필요한 어디든지 데이터베이스에 FK를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-253">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="b0a5f-254">EF Core는 자동으로 만들어진 FK에 대한 [섀도 속성](/ef/core/modeling/shadow-properties)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-254">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="b0a5f-255">데이터 모델에 FK가 있으면 더 간단하고 더 효율적으로 업데이트를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-255">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="b0a5f-256">예를 들어 FK 키 속성 `DepartmentID`가 포함되지 *않은* 모델을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-256">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="b0a5f-257">과정 엔터티가 편집을 위해 페치되는 경우:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-257">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="b0a5f-258">`Department` 엔터티는 명시적으로 로드되지 않은 경우 Null입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-258">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="b0a5f-259">과정 엔터티를 업데이트하려면 `Department` 엔터티를 먼저 페치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-259">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="b0a5f-260">FK 속성 `DepartmentID`가 데이터 모델에 포함된 경우 업데이트하기 전에 `Department` 엔터티를 페치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-260">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="b0a5f-261">DatabaseGenerated 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-261">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="b0a5f-262">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 특성은 PK가 데이터베이스에서 생성되지 않고 애플리케이션에서 제공되는 것을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-262">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="b0a5f-263">기본적으로 EF Core는 PK 값이 DB에서 생성되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-263">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="b0a5f-264">DB에서 생성된 PK 값은 일반적으로 가장 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-264">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="b0a5f-265">`Course` 엔터티의 경우 사용자는 PK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-265">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="b0a5f-266">예를 들어 수학 부서에 대한 1000 시리즈, 영어 부서에 대한 2000 시리즈와 같은 강좌 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-266">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="b0a5f-267">`DatabaseGenerated` 특성은 기본 값을 생성하는 데 사용될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-267">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="b0a5f-268">예를 들어 DB는 행이 만들어지거나 업데이트된 날짜를 기록하기 위해 날짜 필드를 자동으로 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-268">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="b0a5f-269">자세한 내용은 [생성된 속성](/ef/core/modeling/generated-properties)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-269">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b0a5f-270">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-270">Foreign key and navigation properties</span></span>

<span data-ttu-id="b0a5f-271">`Course` 엔터티의 FK(외래 키) 속성 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-271">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="b0a5f-272">강좌는 하나의 부서에 할당되었으므로 `DepartmentID` FK 및 `Department` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-272">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="b0a5f-273">강좌에는 등록된 학생이 여러 명 있을 수 있으므로 `Enrollments` 탐색 속성은 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-273">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="b0a5f-274">여러 강사가 한 강좌를 수업할 수 있으므로 `CourseAssignments` 탐색 속성은 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-274">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="b0a5f-275">`CourseAssignment`는 [나중에](#many-to-many-relationships) 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-275">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="b0a5f-276">부서 엔터티 만들기</span><span class="sxs-lookup"><span data-stu-id="b0a5f-276">Create the Department entity</span></span>

![부서 엔터티](complex-data-model/_static/department-entity.png)

<span data-ttu-id="b0a5f-278">다음 코드로 *Models/Department.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-278">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="b0a5f-279">열 특성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-279">The Column attribute</span></span>

<span data-ttu-id="b0a5f-280">이전에 `Column` 특성은 열 이름 매핑을 변경하는 데 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-280">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="b0a5f-281">`Department` 엔터티에 대한 코드에서 `Column` 특성은 SQL 데이터 형식 매핑을 변경하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-281">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="b0a5f-282">`Budget` 열은 DB에서 SQL Server money 형식을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-282">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="b0a5f-283">열 매핑은 일반적으로 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-283">Column mapping is generally not required.</span></span> <span data-ttu-id="b0a5f-284">EF Core는 일반적으로 속성에 대한 CLR 형식에 따라 적절한 SQL Server 데이터 형식을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-284">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="b0a5f-285">CLR `decimal` 형식은 SQL Server `decimal` 유형에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-285">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="b0a5f-286">`Budget`은 통화에 대한 것이고 money 데이터 형식은 통화에 더욱 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-286">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b0a5f-287">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-287">Foreign key and navigation properties</span></span>

<span data-ttu-id="b0a5f-288">FK 및 탐색 속성은 다음과 같은 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-288">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="b0a5f-289">부서는 관리자를 갖거나 갖지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-289">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="b0a5f-290">관리자는 항상 강사입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-290">An administrator is always an instructor.</span></span> <span data-ttu-id="b0a5f-291">따라서 `InstructorID` 속성은 `Instructor` 엔터티에 FK로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-291">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="b0a5f-292">탐색 속성이 `Administrator`로 명명되나, `Instructor` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-292">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="b0a5f-293">위의 코드에서 물음표(?)는 속성이 nullable임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-293">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="b0a5f-294">부서에는 강좌가 많이 있을 수 있으므로 강좌 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-294">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="b0a5f-295">참고: 규칙에 따라 EF Core는 Null을 허용하지 않는 FK 및 다대다 관계에 대한 계단식 삭제를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-295">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="b0a5f-296">계단식 삭제로 인해 순환 계단식 삭제 규칙이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-296">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="b0a5f-297">순환 계단식 삭제 규칙은 마이그레이션이 추가될 때 예외를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-297">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="b0a5f-298">예를 들어 `Department.InstructorID` 속성이 null 허용 안 함으로 정의된 경우:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-298">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="b0a5f-299">EF Core는 강사가 삭제될 때 부서를 삭제하도록 계단식 삭제 규칙을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-299">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="b0a5f-300">강사가 삭제될 때 부서 삭제는 의도된 동작이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-300">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="b0a5f-301">다음 흐름 API는 계단식 대신 제한 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-301">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="b0a5f-302">위의 코드는 부서 강사 관계에서 계단식 삭제를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-302">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="b0a5f-303">등록 엔터티 업데이트</span><span class="sxs-lookup"><span data-stu-id="b0a5f-303">Update the Enrollment entity</span></span>

<span data-ttu-id="b0a5f-304">등록 레코드는 한 명의 학생이 수행하는 하나의 강좌에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-304">An enrollment record is for one course taken by one student.</span></span>

![등록 엔터티](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="b0a5f-306">*Models/Enrollment.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-306">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b0a5f-307">외래 키 및 탐색 속성</span><span class="sxs-lookup"><span data-stu-id="b0a5f-307">Foreign key and navigation properties</span></span>

<span data-ttu-id="b0a5f-308">FK 속성 및 탐색 속성은 다음 관계를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="b0a5f-309">등록 레코드는 하나의 강좌에 해당하므로 `CourseID` FK 속성 및 `Course` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="b0a5f-310">등록 레코드는 한 명의 학생에 해당하므로 `StudentID` FK 속성 및 `Student` 탐색 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="b0a5f-311">다대다 관계</span><span class="sxs-lookup"><span data-stu-id="b0a5f-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="b0a5f-312">`Student` 및 `Course` 엔터티 사이에 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="b0a5f-313">`Enrollment` 엔터티는 데이터베이스에서 *페이로드를 사용하여* 다대다 조인 테이블로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-313">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="b0a5f-314">"페이로드 사용"은 `Enrollment` 테이블이 조인된 테이블에 대한 FK 외에도 추가 데이터를 포함하는 것을 의미합니다(이 경우 PK 및 `Grade`).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-314">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="b0a5f-315">다음 그림은 이러한 관계 모양을 엔터티 다이어그램으로 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-315">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="b0a5f-316">(이 다이어그램은 EF 6.x에 대한 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)를 사용하여 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-316">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="b0a5f-317">다이어그램 만들기는 자습서의 일부가 아닙니다.)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-317">Creating the diagram isn't part of the tutorial.)</span></span>

![학생-강좌 다대다 관계](complex-data-model/_static/student-course.png)

<span data-ttu-id="b0a5f-319">각 관계 줄에는 한쪽 끝에 1, 다른 한쪽 끝에는 별표(\*)가 있으며, 이는 일대다 관계를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-319">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="b0a5f-320">`Enrollment` 테이블에 등급 정보가 포함되지 않은 경우 두 개의 FK(`CourseID` 및 `StudentID`)를 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-320">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="b0a5f-321">페이로드가 없는 다대다 조인 테이블은 PJT(순수 조인 테이블)라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-321">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="b0a5f-322">`Instructor` 및 `Course` 엔터티에는 순수 조인 테이블을 사용하는 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-322">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="b0a5f-323">참고: EF 6.x는 다대다 관계에 대한 암시적 조인 테이블을 지원하지만 EF Core는 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-323">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="b0a5f-324">자세한 내용은 [EF Core 2.0에서 다대다 관계](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-324">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="b0a5f-325">CourseAssignment 엔터티</span><span class="sxs-lookup"><span data-stu-id="b0a5f-325">The CourseAssignment entity</span></span>

![CourseAssignment 엔터티](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="b0a5f-327">다음 코드로 *Models/CourseAssignment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-327">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="b0a5f-328">강사-강좌</span><span class="sxs-lookup"><span data-stu-id="b0a5f-328">Instructor-to-Courses</span></span>

![강사-강좌 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b0a5f-330">강사-강좌 다대다 관계:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-330">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="b0a5f-331">엔터티 집합으로 표현되어야 하는 조인 테이블이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-331">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="b0a5f-332">순수 조인 테이블(페이로드 없는 테이블)입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-332">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="b0a5f-333">조인 엔터티 `EntityName1EntityName2`의 이름을 지정하는 데 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-333">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="b0a5f-334">예를 들어 이 패턴을 사용하는 강사-강좌 조인 테이블은 `CourseInstructor`입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-334">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="b0a5f-335">그러나 관계를 설명하는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-335">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="b0a5f-336">데이터 모델은 단순하게 시작하고 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-336">Data models start out simple and grow.</span></span> <span data-ttu-id="b0a5f-337">페이로드 없는 조인(PJT)은 페이로드를 포함하도록 자주 발전합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-337">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="b0a5f-338">설명이 포함된 엔터티 이름으로 시작하여 이름은 조인 테이블이 변경될 때 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-338">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="b0a5f-339">이상적으로 조인 엔터티는 비즈니스 도메인에서 고유의 자연스러운(가능한 한 단어) 이름을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-339">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="b0a5f-340">예를 들어 Books 및 Customers는 Ratings라는 조인 엔터티를 통해 연결될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-340">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="b0a5f-341">강사-강좌 다대다 관계의 경우 `CourseAssignment`는 `CourseInstructor`보다 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-341">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="b0a5f-342">복합 키</span><span class="sxs-lookup"><span data-stu-id="b0a5f-342">Composite key</span></span>

<span data-ttu-id="b0a5f-343">FK는 Null을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-343">FKs are not nullable.</span></span> <span data-ttu-id="b0a5f-344">`CourseAssignment`에서 두 개의 FK(`InstructorID` 및 `CourseID`)는 함께 `CourseAssignment` 테이블의 각 행을 고유하게 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-344">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="b0a5f-345">`CourseAssignment`는 전용 PK가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-345">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="b0a5f-346">`InstructorID` 및 `CourseID` 속성은 복합 PK로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-346">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="b0a5f-347">복합 PK를 EF Core로 지정하는 유일한 방법은 *흐름 API*를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-347">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="b0a5f-348">다음 섹션에서는 복합 PK를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-348">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="b0a5f-349">복합 키는 다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-349">The composite key ensures:</span></span>

* <span data-ttu-id="b0a5f-350">하나의 강좌에 대해 여러 행이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-350">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="b0a5f-351">한 명의 강사에 대해 여러 행이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-351">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="b0a5f-352">동일한 강사 및 강좌에 대한 여러 행은 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-352">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="b0a5f-353">`Enrollment` 조인 엔터티는 고유한 PK를 정의하므로 이러한 종류의 중복이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-353">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="b0a5f-354">이러한 중복 항목을 방지하려면:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-354">To prevent such duplicates:</span></span>

* <span data-ttu-id="b0a5f-355">FK 필드에 고유 인덱스를 추가하거나</span><span class="sxs-lookup"><span data-stu-id="b0a5f-355">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="b0a5f-356">`CourseAssignment`와 유사한 기본 복합 키로 `Enrollment`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-356">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="b0a5f-357">자세한 내용은 [인덱스](/ef/core/modeling/indexes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-357">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="b0a5f-358">DB 컨텍스트 업데이트</span><span class="sxs-lookup"><span data-stu-id="b0a5f-358">Update the DB context</span></span>

<span data-ttu-id="b0a5f-359">다음 강조 표시된 코드를 *Data/SchoolContext.cs*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-359">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="b0a5f-360">위의 코드는 새 엔터티를 추가하고 `CourseAssignment` 엔터티의 복합 PK를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-360">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="b0a5f-361">특성에 대한 흐름 API 대안</span><span class="sxs-lookup"><span data-stu-id="b0a5f-361">Fluent API alternative to attributes</span></span>

<span data-ttu-id="b0a5f-362">위의 코드에서 `OnModelCreating` 메서드는 *흐름 API*를 사용하여 EF Core 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-362">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="b0a5f-363">API는 종종 일련의 메서드 호출을 단일 명령문으로 함께 연결하여 사용되기 때문에 “흐름”이라고 부릅니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-363">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="b0a5f-364">[다음 코드](/ef/core/modeling/#use-fluent-api-to-configure-a-model)는 흐름 API의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-364">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="b0a5f-365">이 자습서에서 흐름 API는 특성으로 수행될 수 없는 DB 매핑을 위해서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-365">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="b0a5f-366">그러나 흐름 API는 특성으로 수행될 수 있는 대부분의 서식 지정, 유효성 검사 및 매핑 규칙을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-366">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="b0a5f-367">`MinimumLength`와 같은 일부 특성은 흐름 API를 통해 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-367">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="b0a5f-368">`MinimumLength`는 스키마를 변경하지 않으며 최소 길이 유효성 검사 규칙만 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-368">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="b0a5f-369">일부 개발자는 흐름 API를 단독으로 사용하는 것을 선호하므로 자신의 엔터티 클래스를 “정리”할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-369">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="b0a5f-370">특성 및 흐름 API를 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-370">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="b0a5f-371">흐름 API로만 수행될 수 있는 일부 구성이 있습니다(복합 PK 지정).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-371">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="b0a5f-372">특성으로만 수행될 수 있는 일부 구성이 있습니다(`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-372">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="b0a5f-373">흐름 API 또는 특성을 사용하기 위한 권장 방법:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-373">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="b0a5f-374">이 두 가지 방법 중 하나를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-374">Choose one of these two approaches.</span></span>
* <span data-ttu-id="b0a5f-375">가능한 한 계속 선택한 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-375">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="b0a5f-376">이 자습서에서 사용되는 일부 특성은 다음에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-376">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="b0a5f-377">유효성 검사에만(예: `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-377">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="b0a5f-378">EF Core 구성에만(예: `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-378">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="b0a5f-379">유효성 검사 및 EF Core 구성(예: `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-379">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="b0a5f-380">특성과 흐름 API의 비교에 대한 자세한 내용은 [구성 메서드](/ef/core/modeling/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-380">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="b0a5f-381">관계를 보여 주는 엔터티 다이어그램</span><span class="sxs-lookup"><span data-stu-id="b0a5f-381">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="b0a5f-382">다음 그림은 EF Power Tools가 완벽한 School 모델을 만드는 다이어그램을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-382">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![엔터티 다이어그램](complex-data-model/_static/diagram.png)

<span data-ttu-id="b0a5f-384">위의 다이어그램은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-384">The preceding diagram shows:</span></span>

* <span data-ttu-id="b0a5f-385">여러 일대다 관계 줄(1 ~ \*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-385">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="b0a5f-386">`Instructor` 및 `OfficeAssignment` 엔터티 사이에는 일대영 또는 일 관계 줄(1 ~ 0..1)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-386">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="b0a5f-387">`Instructor` 및 `Department` 엔터티 사이에는 영 또는 일대다 관계 줄(0..1 ~ \*)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-387">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="b0a5f-388">테스트 데이터로 DB 시드</span><span class="sxs-lookup"><span data-stu-id="b0a5f-388">Seed the DB with Test Data</span></span>

<span data-ttu-id="b0a5f-389">*Data/DbInitializer.cs*에서 코드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-389">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="b0a5f-390">위의 코드는 새 엔터티에 대한 시드 데이터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-390">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="b0a5f-391">이 코드의 대부분은 새 엔터티 개체를 만들고 샘플 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-391">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="b0a5f-392">샘플 데이터는 테스트를 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-392">The sample data is used for testing.</span></span> <span data-ttu-id="b0a5f-393">다 대 다 조인 테이블을 시드할 수 있는 예제는 `Enrollments` 및 `CourseAssignments`를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-393">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="b0a5f-394">마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="b0a5f-394">Add a migration</span></span>

<span data-ttu-id="b0a5f-395">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-395">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b0a5f-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0a5f-396">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b0a5f-397">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b0a5f-397">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="b0a5f-398">위의 명령은 가능한 데이터 손실에 대한 경고를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-398">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="b0a5f-399">`database update` 명령이 실행되는 경우 다음과 같은 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-399">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="b0a5f-400">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="b0a5f-400">Apply the migration</span></span>

<span data-ttu-id="b0a5f-401">기존 데이터베이스가 있으므로 향후 변경 내용을 적용하는 방법을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-401">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="b0a5f-402">이 자습서에서는 두 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-402">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="b0a5f-403">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="b0a5f-403">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="b0a5f-404">[기존 데이터베이스에 마이그레이션 적용](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-404">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="b0a5f-405">이 방법은 더 복잡하고 시간이 오래 걸리지만 실제 프로덕션 환경에 권장되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-405">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="b0a5f-406">**참고**: 이는 자습서의 선택적 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-406">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="b0a5f-407">삭제하고 다시 만들기 단계를 수행하고 이 섹션을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-407">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="b0a5f-408">이 섹션의 단계를 수행하지 않으려면 삭제하고 다시 만들기 단계를 수행하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-408">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="b0a5f-409">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="b0a5f-409">Drop and re-create the database</span></span>

<span data-ttu-id="b0a5f-410">업데이트된 `DbInitializer`의 코드는 새 엔터티에 대한 시드 데이터를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-410">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="b0a5f-411">EF Core가 새로운 DB를 만들도록 강제하려면 DB를 삭제하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-411">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b0a5f-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0a5f-412">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0a5f-413">PMC(**패키지 관리자 콘솔**)에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-413">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="b0a5f-414">PMC에서 `Get-Help about_EntityFrameworkCore`를 실행하여 도움말 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-414">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b0a5f-415">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b0a5f-415">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b0a5f-416">명령 창을 열고 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-416">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="b0a5f-417">프로젝트 폴더에는 *Startup.cs* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-417">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="b0a5f-418">명령 창에서 다음을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-418">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="b0a5f-419">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-419">Run the app.</span></span> <span data-ttu-id="b0a5f-420">앱을 실행하면 `DbInitializer.Initialize` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-420">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="b0a5f-421">`DbInitializer.Initialize`는 새 DB를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-421">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="b0a5f-422">SSOX에서 DB를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-422">Open the DB in SSOX:</span></span>

* <span data-ttu-id="b0a5f-423">SSOX가 이전에 열려 있던 경우 **새로 고침** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-423">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="b0a5f-424">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-424">Expand the **Tables** node.</span></span> <span data-ttu-id="b0a5f-425">생성된 테이블이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-425">The created tables are displayed.</span></span>

![SSOX의 테이블](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="b0a5f-427">**CourseAssignment** 테이블을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-427">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="b0a5f-428">**CourseAssignment** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-428">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="b0a5f-429">**CourseAssignment** 테이블에 데이터가 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-429">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX의 CourseAssignment 데이터](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="b0a5f-431">기존 데이터베이스에 마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="b0a5f-431">Apply the migration to the existing database</span></span>

<span data-ttu-id="b0a5f-432">이 섹션은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-432">This section is optional.</span></span> <span data-ttu-id="b0a5f-433">이러한 단계는 이전의 [데이터베이스를 삭제하고 다시 만들기](#drop) 섹션을 건너뛴 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-433">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="b0a5f-434">마이그레이션이 기존 데이터로 실행될 때 기존 데이터로 충족되지 않는 FK 제약 조건이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="b0a5f-435">프로덕션 데이터와 함께 기존 데이터를 마이그레이션하도록 단계를 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="b0a5f-436">이 섹션에서는 FK 제약 조건 위반을 수정하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="b0a5f-437">백업 없이 이러한 코드 변경을 만들지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="b0a5f-438">이전 섹션을 완료하고 데이터베이스를 업데이트한 경우 이러한 코드 변경을 만들지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-438">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="b0a5f-439">*{timestamp}_ComplexDataModel.cs* 파일은 다음 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="b0a5f-440">위의 코드는 Null을 허용하지 않는 `DepartmentID` FK를 `Course` 테이블에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="b0a5f-441">테이블을 마이그레이션에서 업데이트할 수 없도록 이전 자습서의 DB는 `Course`에 행을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-441">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="b0a5f-442">기존 데이터를 사용하여 `ComplexDataModel` 마이그레이션 작업을 수행하려면:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="b0a5f-443">새 열(`DepartmentID`)에 기본값을 제공하도록 코드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="b0a5f-444">기본 부서로 작동하도록 "Temp"라는 가짜 부서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="b0a5f-445">외래 키 제약 조건 수정</span><span class="sxs-lookup"><span data-stu-id="b0a5f-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="b0a5f-446">`ComplexDataModel` 클래스 `Up` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-446">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="b0a5f-447">*{timestamp}_ComplexDataModel.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="b0a5f-448">`DepartmentID` 열을 `Course` 테이블에 추가하는 코드 줄을 주석으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="b0a5f-449">다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-449">Add the following highlighted code.</span></span> <span data-ttu-id="b0a5f-450">새 코드는 `.CreateTable( name: "Department"` 블록 뒤로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="b0a5f-451">위의 변경 내용으로 `ComplexDataModel` `Up` 메서드를 실행한 후에 기존 `Course` 행은 “Temp” 부서에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-451">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="b0a5f-452">프로덕션 앱은:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-452">A production app would:</span></span>

* <span data-ttu-id="b0a5f-453">`Department` 행 및 관련 `Course` 행을 새 `Department` 행에 추가하는 코드 또는 스크립트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-453">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="b0a5f-454">`Course.DepartmentID`에 대해 "Temp" 부서 또는 기본값을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-454">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="b0a5f-455">다음 자습서에서는 관련된 데이터를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-455">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0a5f-456">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b0a5f-456">Additional resources</span></span>

* [<span data-ttu-id="b0a5f-457">이 자습서의 YouTube 버전(1부)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-457">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="b0a5f-458">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-458">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> <span data-ttu-id="b0a5f-459">[이전](xref:data/ef-rp/migrations)
> [다음](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-459">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>
