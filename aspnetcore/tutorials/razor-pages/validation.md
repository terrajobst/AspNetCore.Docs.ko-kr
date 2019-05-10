---
title: ASP.NET Core Razor 페이지에 유효성 검사 추가
author: rick-anderson
description: ASP.NET Core의 Razor 페이지에 유효성 검사를 추가하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/validation
ms.openlocfilehash: e9214139c0e6e958445feb13b6350bad376a0152
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884168"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="c015e-103">ASP.NET Core Razor 페이지에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="c015e-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="c015e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c015e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c015e-105">이 섹션에서 유효성 검사 논리는 `Movie` 모델에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="c015e-106">유효성 검사 규칙은 사용자가 동영상을 만들거나 편집할 때 언제든지 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="c015e-107">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="c015e-107">Validation</span></span>

<span data-ttu-id="c015e-108">소프트웨어 개발의 주요 개념은 [반복 금지](https://wikipedia.org/wiki/Don%27t_repeat_yourself)라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="c015e-109">Razor 페이지는 기능이 한번 정의된 곳의 개발을 원활하게 하고 앱 전체에서 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="c015e-110">DRY는 다음 작업에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-110">DRY can help:</span></span>

* <span data-ttu-id="c015e-111">앱에서 코드의 양 감소</span><span class="sxs-lookup"><span data-stu-id="c015e-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="c015e-112">코드에 오류 감소 및 쉽게 테스트 및 유지 관리</span><span class="sxs-lookup"><span data-stu-id="c015e-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="c015e-113">Razor 페이지에서 제공하는 유효성 검사 지원 및 Entity Framework는 반복 금지 원칙의 좋은 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="c015e-114">유효성 검사 규칙은 한 위치(모델 클래스에서)에서 선언적으로 지정되고 규칙은 앱의 모든 위치에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

### <a name="adding-validation-rules-to-the-movie-model"></a><span data-ttu-id="c015e-115">동영상 모델에 유효성 검사 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="c015e-115">Adding validation rules to the movie model</span></span>

<span data-ttu-id="c015e-116">*Models/Movie.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-116">Open the *Models/Movie.cs* file.</span></span> <span data-ttu-id="c015e-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) provides a built-in set of validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="c015e-118">또한 DataAnnotations는 서식 지정을 돕는 `DataType`과 같은 서식 지정 특성을 포함하며 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-118">DataAnnotations also contain formatting attributes like `DataType` that help with formatting and don't provide validation.</span></span>

<span data-ttu-id="c015e-119">`Required`, `StringLength`, `RegularExpression` 및 `Range` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-119">Update the `Movie` class to take advantage of the `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="c015e-120">유효성 검사 특성은 모델 속성에 적용되는 동작을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-120">Validation attributes specify behavior that's enforced on model properties:</span></span>

* <span data-ttu-id="c015e-121">`Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-121">The `Required` and `MinimumLength` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="c015e-122">그러나 사용자는 nullable 형식에 대한 유효성 검사 제약 조건을 만족하기 위해 공백을 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-122">However, nothing prevents a user from entering whitespace to satisfy the validation constraint for a nullable type.</span></span> <span data-ttu-id="c015e-123">nullable 형식이 아닌 [값 형식](/dotnet/csharp/language-reference/keywords/value-types)(`decimal`, `int`, `float`,`DateTime`)은 기본적으로 필요하며 `Required` 특성은 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-123">Non-nullable [value types](/dotnet/csharp/language-reference/keywords/value-types) (such as `decimal`, `int`, `float`, and `DateTime`) are inherently required and don't need the `Required` attribute.</span></span>
* <span data-ttu-id="c015e-124">`RegularExpression` 특성은 사용자가 입력할 수 있는 문자를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-124">The `RegularExpression` attribute limits the characters that the user can enter.</span></span> <span data-ttu-id="c015e-125">위의 코드에서 `Genre`는 하나 이상의 대문자로 시작하고 0개 이상의 문자, 작은따옴표 또는 큰따옴표, 공백 문자 또는 대시를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-125">In the preceding code, `Genre` must start with one or more capital letters and follow with zero or more letters, single or double quotes, whitespace characters, or dashes.</span></span> <span data-ttu-id="c015e-126">`Rating`은 하나 이상의 대문자로 시작하고 0개 이상의 문자, 숫자, 작은따옴표 또는 큰따옴표, 공백 문자 또는 대시를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-126">`Rating` must start with one or more capital letters and follow with zero or more letters, numbers, single or double quotes, whitespace characters, or dashes.</span></span>
* <span data-ttu-id="c015e-127">`Range` 특성은 지정된 범위로 값을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-127">The `Range` attribute constrains a value to a specified range.</span></span>
* <span data-ttu-id="c015e-128">`StringLength` 특성은 문자열의 최대 길이 및 필요에 따라 최소 길이를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-128">The `StringLength` attribute sets the maximum length of a string, and optionally the minimum length.</span></span> 

<span data-ttu-id="c015e-129">ASP.NET Core에 의해 자동으로 적용되는 유효성 검사 규칙을 사용하면 앱을 보다 강력하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-129">Having validation rules automatically enforced by ASP.NET Core helps make an app more robust.</span></span> <span data-ttu-id="c015e-130">모델에서 자동 유효성 검사는 새 코드가 추가될 때 적용하기 위해 기억할 필요가 없기 때문에 앱을 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-130">Automatic validation on models helps protect the app because you don't have to remember to apply them when new code is added.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="c015e-131">Razor 페이지에서 유효성 검사 오류 UI</span><span class="sxs-lookup"><span data-stu-id="c015e-131">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="c015e-132">앱을 실행하고 Pages/Movies로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-132">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="c015e-133">**새로 만들기** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-133">Select the **Create New** link.</span></span> <span data-ttu-id="c015e-134">일부 잘못된 값으로 양식을 완성합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-134">Complete the form with some invalid values.</span></span> <span data-ttu-id="c015e-135">jQuery 클라이언트 쪽 유효성 검사에서 오류를 발견하면 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-135">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 있는 동영상 보기 양식](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

<span data-ttu-id="c015e-137">양식에서 잘못된 값을 포함하는 각 필드에 유효성 검사 오류 메시지를 자동으로 렌더링하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-137">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="c015e-138">오류는 클라이언트 쪽(JavaScript 및 jQuery 사용) 및 서버 쪽(사용자가 JavaScript를 사용하지 않도록 설정한 경우) 모두 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-138">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="c015e-139">중요한 이점은 만들기 또는 편집 페이지에서 코드 변경이 필요하지 **않았다는** 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-139">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="c015e-140">DataAnnotations가 모델에 적용된 후 유효성 검사 UI가 활성화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-140">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="c015e-141">이 자습서에서 만든 Razor 페이지는 자동으로 유효성 검사 규칙을 선택했습니다(`Movie` 모델 클래스의 속성에서 유효성 검사 특성 사용).</span><span class="sxs-lookup"><span data-stu-id="c015e-141">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="c015e-142">편집 페이지를 사용하는 테스트 유효성 검사는 동일한 유효성 검사가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-142">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="c015e-143">양식 데이터는 클라이언트 쪽 유효성 검사 오류가 없을 때까지 서버에 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-143">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="c015e-144">양식 데이터가 다음 방법 중 하나 이상으로 게시되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-144">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="c015e-145">`OnPostAsync` 메서드에 중단점을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-145">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="c015e-146">양식을 제출합니다(**만들기** 또는 **저장** 선택).</span><span class="sxs-lookup"><span data-stu-id="c015e-146">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="c015e-147">중단점은 적중되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-147">The break point is never hit.</span></span>
* <span data-ttu-id="c015e-148">[Fiddler 도구](http://www.telerik.com/fiddler)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-148">Use the [Fiddler tool](http://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="c015e-149">브라우저 개발자 도구를 사용하여 네트워크 트래픽을 모니터링합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-149">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="c015e-150">서버 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="c015e-150">Server-side validation</span></span>

<span data-ttu-id="c015e-151">브라우저에서 JavaScript가 비활성화된 경우 오류가 있는 양식 제출에서 서버에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-151">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="c015e-152">선택 가능한 서버 쪽 유효성 검사 테스트:</span><span class="sxs-lookup"><span data-stu-id="c015e-152">Optional, test server-side validation:</span></span>

* <span data-ttu-id="c015e-153">브라우저에서 JavaScript를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-153">Disable JavaScript in the browser.</span></span> <span data-ttu-id="c015e-154">브라우저의 개발자 도구를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-154">You can do this using your browser's developer tools.</span></span> <span data-ttu-id="c015e-155">브라우저에서 JavaScript를 비활성화할 수 없는 경우 다른 브라우저를 시도하세요.</span><span class="sxs-lookup"><span data-stu-id="c015e-155">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="c015e-156">만들기 또는 편집 페이지의 `OnPostAsync` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-156">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="c015e-157">유효성 검사 오류가 있는 양식을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-157">Submit a form with validation errors.</span></span>
* <span data-ttu-id="c015e-158">모델 상태가 유효하지 않은 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-158">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="c015e-159">다음 코드는 자습서의 앞부분에서 스캐폴드한 *Create.cshtml* 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-159">The following code shows a portion of the *Create.cshtml* page that you scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="c015e-160">만들기 및 편집 페이지에서 초기 양식을 표시하고 오류가 발생한 경우 양식을 다시 표시하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-160">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="c015e-161">[입력 태그 도우미](xref:mvc/views/working-with-forms)는 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 특성을 사용하고 클라이언트 쪽의 jQuery 유효성 검사에 필요한 HTML 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-161">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="c015e-162">[유효성 검사 태그 도우미](xref:mvc/views/working-with-forms#the-validation-tag-helpers)는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-162">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="c015e-163">자세한 내용은 [유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c015e-163">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="c015e-164">만들기 및 편집 페이지에 유효성 검사 규칙이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-164">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="c015e-165">유효성 검사 규칙 및 오류 문자열은 `Movie` 클래스에서만 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-165">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="c015e-166">이러한 유효성 검사 규칙은 `Movie` 모델을 편집하는 Razor 페이지에 자동으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-166">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="c015e-167">유효성 검사 논리를 변경해야 하는 경우 모델에서만 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-167">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="c015e-168">유효성 검사는 애플리케이션에 걸쳐 일관되게 적용됩니다(유효성 검사 논리는 한 위치에서 정의됨).</span><span class="sxs-lookup"><span data-stu-id="c015e-168">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="c015e-169">한 위치의 유효성 검사를 통해 코드를 깨끗이 유지하고 쉽게 유지 관리하고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-169">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="c015e-170">데이터 형식 특성 사용</span><span class="sxs-lookup"><span data-stu-id="c015e-170">Using DataType Attributes</span></span>

<span data-ttu-id="c015e-171">`Movie` 클래스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-171">Examine the `Movie` class.</span></span> <span data-ttu-id="c015e-172">`System.ComponentModel.DataAnnotations` 네임스페이스는 기본 제공 유효성 검사 특성의 집합 외에 서식 지정 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-172">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="c015e-173">`DataType` 특성은 `ReleaseDate` 및 `Price` 속성에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-173">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="c015e-174">`DataType` 특성은 데이터의 서식을 지정하도록 뷰 엔진에 대한 힌트만을 제공합니다(그리고 URL에 대한 `<a>` 및 전자 메일에 대한 `<a href="mailto:EmailAddress.com">`과 같은 특성 제공).</span><span class="sxs-lookup"><span data-stu-id="c015e-174">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="c015e-175">`RegularExpression` 특성을 사용하여 데이터 형식의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-175">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="c015e-176">`DataType` 특성은 데이터베이스 내장 형식보다 구체적인 데이터 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-176">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="c015e-177">`DataType` 특성은 유효성 검사 특성이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-177">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="c015e-178">샘플 애플리케이션에서 날짜는 시간 없이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-178">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="c015e-179">`DataType` 열거형은 날짜, 시간, 전화 번호, 통화, 전자 메일 주소 등과 같은 많은 데이터 형식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-179">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="c015e-180">`DataType` 특성을 통해 애플리케이션에서 자동으로 유형별 기능을 제공하도록 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-180">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="c015e-181">예를 들어 `DataType.EmailAddress`에 대해 `mailto:` 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-181">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="c015e-182">HTML5를 지원하는 브라우저에서 `DataType.Date`에 대해 날짜 선택기가 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-182">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="c015e-183">`DataType` 특성은 HTML 5 브라우저에서 사용하는 HTML 5 `data-`(데이터 대시로 발음) 특성을 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-183">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="c015e-184">`DataType` 특성은 유효성 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="c015e-184">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="c015e-185">`DataType.Date`는 표시되는 날짜의 서식을 지정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-185">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="c015e-186">기본적으로 데이터 필드는 서버 `CultureInfo`의 기본 형식에 따라 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-186">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="c015e-187">Entity Framework Core가 `Price`를 데이터베이스의 통화에 올바르게 매핑할 수 있도록 `[Column(TypeName = "decimal(18, 2)")]` 데이터 주석이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-187">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="c015e-188">자세한 내용은 [데이터 형식](/ef/core/modeling/relational/data-types)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c015e-188">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="c015e-189">`DisplayFormat` 특성은 날짜 형식을 명시적으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-189">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="c015e-190">`ApplyFormatInEditMode` 설정은 값이 편집을 위해 표시될 때 서식 지정이 적용되어야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-190">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="c015e-191">일부 필드에 대해 해당 동작을 원하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-191">You might not want that behavior for some fields.</span></span> <span data-ttu-id="c015e-192">예를 들어 통화 값의 편집 UI에서 통화 기호를 원하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-192">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="c015e-193">`DisplayFormat` 특성은 단독으로 사용될 수 있지만 일반적으로 `DataType` 특성을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-193">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="c015e-194">`DataType` 특성은 데이터를 화면에 렌더링하는 방법과 반대로 데이터의 의미 체계를 전달하고 DisplayFormat으로 가져올 수 없는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-194">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="c015e-195">브라우저는 HTML5 기능을 활성화할 수 있습니다(예: 달력 컨트롤, 로캘에 적합한 통화 기호, 전자 메일 링크 등을 표시하기 위해).</span><span class="sxs-lookup"><span data-stu-id="c015e-195">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="c015e-196">기본적으로 브라우저는 사용자의 로캘에 따른 올바른 형식을 사용하여 데이터를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-196">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="c015e-197">`DataType` 특성은 ASP.NET Core 프레임워크를 활성화하여 데이터를 렌더링할 올바른 필드 템플릿을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-197">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="c015e-198">자체적으로 사용되는 경우 `DisplayFormat`은 문자열 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-198">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="c015e-199">참고: jQuery 유효성 검사는 `Range` 특성 및 `DateTime`을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-199">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="c015e-200">예를 들어 다음 코드는 날짜가 지정된 범위에 있을 경우에도 클라이언트 쪽 유효성 검사 오류를 항상 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-200">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="c015e-201">일반적으로 모델에서 하드 날짜를 컴파일하는 것은 좋지 않으므로 `Range` 특성 및 `DateTime`을 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-201">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="c015e-202">다음 코드는 한 줄에 결합 특성을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-202">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="c015e-203">[Razor Pages 및 EF Core 시작](xref:data/ef-rp/intro)에서는 Razor Pages를 사용한 고급 EF Core 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-203">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="publish-to-azure"></a><span data-ttu-id="c015e-204">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="c015e-204">Publish to Azure</span></span>

<span data-ttu-id="c015e-205">Azure에 배포하는 방법에 대한 자세한 내용은 [자습서: SQL Database가 포함된 Azure에서 ASP.NET 앱 빌드](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c015e-205">For information on deploying to Azure, see [Tutorial: Build an ASP.NET app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase).</span></span> <span data-ttu-id="c015e-206">ASP.NET Core 앱이 아닌 ASP.NET 앱에 대한 지침이지만 단계는 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-206">These instructions are for an ASP.NET app, not an ASP.NET Core app, but the steps are the same.</span></span>

<span data-ttu-id="c015e-207">Razor 페이지에 대한 이 소개를 완료해 주셔서 감사합니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-207">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="c015e-208">[Razor Pages 및 EF Core 시작](xref:data/ef-rp/intro)은 이 자습서의 유용한 후속편입니다.</span><span class="sxs-lookup"><span data-stu-id="c015e-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c015e-209">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c015e-209">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="c015e-210">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="c015e-210">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="c015e-211">이전: 새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="c015e-211">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
