---
title: ASP.NET Core MVC의 모델 유효성 검사
author: rick-anderson
description: ASP.NET Core MVC 및 Razor Pages의 모델 유효성 검사에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: a39eeead10849d11349688c42fe814ede9e8a847
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172497"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="39af2-103">ASP.NET Core MVC 및 Razor Pages의 모델 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39af2-104">[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="39af2-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="39af2-105">이 문서에서는 ASP.NET Core MVC 또는 Razor Pages 앱에서 사용자 입력의 유효성을 검사하는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="39af2-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="39af2-107">모델 상태</span><span class="sxs-lookup"><span data-stu-id="39af2-107">Model state</span></span>

<span data-ttu-id="39af2-108">모델 상태는 모델 바인딩 및 모델 유효성 검사 두 하위 시스템에서 발생하는 오류를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="39af2-109">[모델 바인딩](model-binding.md)에서 발생하는 오류는 일반적으로 데이터 변환 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="39af2-110">예를 들어 정수 필드에 “x”가 입력됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="39af2-111">모델 유효성 검사는 모델 바인딩 후에 발생하며 데이터가 비즈니스 규칙에 맞지 않으면 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="39af2-112">예를 들어 1에서 5 사이의 등급을 필요한 필드에 0이 입력됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="39af2-113">모델 바인딩과 모델 유효성 검사 모두 컨트롤러 작업 또는 Razor Pages 처리기 메서드를 실행하기 전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="39af2-114">웹앱의 경우 `ModelState.IsValid`를 검사하고 적절히 반응하는 기능은 앱에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="39af2-115">일반적으로 웹앱은 페이지를 오류 메시지와 함께 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="39af2-116">웹 API 컨트롤러는 `[ApiController]` 특성을 포함하는 경우 `ModelState.IsValid`를 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="39af2-117">이 경우 모델 상태가 잘못되면 오류 세부 정보를 포함하는 자동 HTTP 400 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="39af2-118">자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="39af2-119">유효성 검사 다시 실행</span><span class="sxs-lookup"><span data-stu-id="39af2-119">Rerun validation</span></span>

<span data-ttu-id="39af2-120">유효성 검사는 자동으로 실행 되지만 수동으로 반복하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="39af2-121">예를 들어 속성에 대한 값을 계산하고 해당 속성을 계산된 값으로 설정한 후 유효성 검사를 다시 실행하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="39af2-122">유효성 검사를 다시 실행하려면 다음과 같이 `TryValidateModel` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="39af2-123">유효성 검사 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-123">Validation attributes</span></span>

<span data-ttu-id="39af2-124">유효성 검사 특성을 사용하여 모델 속성에 대한 유효성 검사 규칙을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="39af2-125">샘플 앱의 다음 예제는 유효성 검사 특성으로 주석을 단 모델 클래스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="39af2-126">`[ClassicMovie]` 특성은 사용자 지정 유효성 검사 특성이며 다른 특성은 기본 제공 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="39af2-127">`[ClassicMovieWithClientValidator]`는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="39af2-128">`[ClassicMovieWithClientValidator]`는 사용자 지정 특성을 구현하는 다른 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="39af2-129">기본 제공 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-129">Built-in attributes</span></span>

<span data-ttu-id="39af2-130">다음은 몇 가지 기본 제공 유효성 검사 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="39af2-131">`[CreditCard]`: 속성에 신용 카드 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="39af2-132">`[Compare]`: 모델의 두 속성이 일치하는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="39af2-133">`[EmailAddress]`: 속성에 이메일 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="39af2-134">`[Phone]`: 속성에 전화 번호 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="39af2-135">`[Range]`: 속성 값이 지정된 범위 내에 포함되는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="39af2-136">`[RegularExpression]`: 속성 값이 지정된 정규 식과 일치하는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="39af2-137">`[Required]`: 필드가 Null이 아닌지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="39af2-138">이 특성의 동작에 대한 자세한 내용은 [`[Required]` 특성](#required-attribute)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="39af2-139">`[StringLength]`: 문자열 속성 값이 지정된 길이 한계를 초과하지 않는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="39af2-140">`[Url]`: 속성에 URL 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="39af2-141">`[Remote]`: 서버에 대한 작업 명령을 호출하여 클라이언트에 대한 입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="39af2-142">이 특성의 동작에 대한 자세한 내용은 [`[Remote]` 특성](#remote-attribute)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="39af2-143">유효성 검사 특성의 전체 목록은 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 네임스페이스에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="39af2-144">오류 메시지</span><span class="sxs-lookup"><span data-stu-id="39af2-144">Error messages</span></span>

<span data-ttu-id="39af2-145">유효성 검사 특성을 사용하여 잘못된 입력에 대해 표시할 오류 메시지를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="39af2-146">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="39af2-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="39af2-147">내부적으로 이 특성은 필드 이름에 대한 자리 표시자 및 경우에 따라 추가 자리 표시자를 사용하여 `String.Format`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="39af2-148">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="39af2-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="39af2-149">`Name` 속성에 적용할 경우 위의 코드에 의해 생성되는 오류 메시지는 "이름의 길이는 6문자에서 8문자 사이여야 합니다"일 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="39af2-150">특정 특성의 오류 메시지에 대해 `String.Format` 에 전달되는 매개 변수를 알아보려면 [DataAnnotations 소스 코드](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="39af2-151">[Required] 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-151">[Required] attribute</span></span>

<span data-ttu-id="39af2-152">.NET Core 3.0 이상의 유효성 검사 시스템은 Null을 허용하지 않는 매개 변수 또는 바인딩된 속성을 `[Required]` 특성을 포함한 것처럼 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-152">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="39af2-153">`decimal` 및 `int`와 같은 [값 형식](/dotnet/csharp/language-reference/keywords/value-types)은 Null을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="39af2-154">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes>를 구성하여 이 동작을 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-154">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="39af2-155">서버에 대한 [Required] 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-155">[Required] validation on the server</span></span>

<span data-ttu-id="39af2-156">서버에서 속성이 Null인 경우 필수 값이 없는 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-156">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="39af2-157">Null을 허용하지 않는 필드는 언제나 유효하며 `[Required]` 특성의 오류 메시지는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-157">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="39af2-158">그러나 Null을 허용하지 않는 속성에 대한 모델 바인딩이 실패하여 `The value '' is invalid`와 같은 오류 메시지가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-158">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="39af2-159">Null을 허용하지 않는 형식의 서버 쪽 유효성 검사에 대한 사용자 지정 오류 메시지를 지정하려는 경우 다음과 같은 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-159">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="39af2-160">필드를 Null 허용으로 만듭니다(예: `decimal` 대신에 `decimal?`).</span><span class="sxs-lookup"><span data-stu-id="39af2-160">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="39af2-161">[Null을 허용하는 \<T>](/dotnet/csharp/programming-guide/nullable-types/) 값 형식은 표준 Nul을 허용 형식처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-161">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="39af2-162">다음 예제에서와 같이 모델 바인딩에 의해 사용할 기본 오류 메시지를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-162">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="39af2-163">기본 메시지를 설정할 수 있는 모델 바인딩 오류에 대한 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="39af2-163">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="39af2-164">클라이언트에 대한 [Required] 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-164">[Required] validation on the client</span></span>

<span data-ttu-id="39af2-165">클라이언트에서 Null을 허용하지 않는 형식 및 문자열은 서버의 경우와 다르게 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-165">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="39af2-166">클라이언트에서:</span><span class="sxs-lookup"><span data-stu-id="39af2-166">On the client:</span></span>

* <span data-ttu-id="39af2-167">값은 해당 값에 대한 입력이 있는 경우에만 존재하는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-167">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="39af2-168">그러므로 클라이언트 쪽 유효성 검사에서는 Null을 허용하지 않는 형식을 Null 허용 형식과 동일하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-168">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="39af2-169">문자열 필드의 공백은 jQuery 유효성 검사 [필수](https://jqueryvalidation.org/required-method/) 메서드에 의해 유효한 입력으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-169">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="39af2-170">서버 쪽 유효성 검사에서는 공백만 입력된 경우 필수 문자열 필드를 잘못된 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-170">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="39af2-171">위에서 설명했듯이 Null을 허용하지 않는 형식은 `[Required]` 특성을 포함한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-171">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="39af2-172">즉, `[Required]` 특성을 적용하지 않더라도 클라이언트 쪽 유효성 검사가 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-172">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="39af2-173">그러나 해당 특성을 사용하지 않으면 기본 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-173">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="39af2-174">사용자 지정 오류 메시지를 지정하려면 해당 특성을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="39af2-174">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="39af2-175">[Remote] 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-175">[Remote] attribute</span></span>

<span data-ttu-id="39af2-176">`[Remote]` 특성은 필드 입력이 유효한지 여부를 결정하기 위해 서버에서 메서드를 호출해야 하는 클라이언트 쪽 유효성 검사를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-176">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="39af2-177">예를 들어 앱에서 사용자 이름을 이미 사용 중인지 여부를 확인해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-177">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="39af2-178">원격 유효성 검사를 구현하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-178">To implement remote validation:</span></span>

1. <span data-ttu-id="39af2-179">JavaScript가 호출할 작업 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-179">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="39af2-180">jQuery 유효성 검사 [remote](https://jqueryvalidation.org/remote-method/) 메서드는 JSON 응답을 수신해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-180">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="39af2-181">`true`는 입력 데이터가 유효함을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-181">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="39af2-182">`false`, `undefined` 또는 `null`은 입력이 잘못되었음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-182">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="39af2-183">기본 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-183">Display the default error message.</span></span>
   * <span data-ttu-id="39af2-184">다른 문자열은 모두 입력이 잘못되었음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-184">Any other string means the input is invalid.</span></span> <span data-ttu-id="39af2-185">문자열을 사용자 지정 오류 메시지로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-185">Display the string as a custom error message.</span></span>

   <span data-ttu-id="39af2-186">다음은 사용자 지정 오류 메시지를 반환하는 작업 메서드의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-186">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="39af2-187">다음 예제와 같이 모델 클래스에서는 유효성 검사 작업 메서드를 가리키는 `[Remote]` 특성으로 속성에 주석을 답니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-187">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="39af2-188">`[Remote]` 네임스페이스에 `Microsoft.AspNetCore.Mvc` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-188">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="39af2-189">추가 필드</span><span class="sxs-lookup"><span data-stu-id="39af2-189">Additional fields</span></span>

<span data-ttu-id="39af2-190">`[Remote]` 특성의 `AdditionalFields` 속성을 사용하여 서버의 데이터를 기준으로 필드 조합의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-190">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="39af2-191">예를 들어 `User` 모델이 `FirstName` 및 `LastName` 속성을 포함한 경우 해당 이름 쌍을 이미 가진 기존 사용자가 없는지 확인하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-191">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="39af2-192">다음 예제에서는 `AdditionalFields`을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-192">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="39af2-193">`AdditionalFields`는 명시적으로 믄자열 "FirstName" 및 "LastName"으로 설정할 수 있지만 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자를 사용하면 나중에 리팩터링이 간단해집니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-193">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="39af2-194">이 유효성 검사의 작업 메서드는 `firstName`과 `lastName` 인수를 모두 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-194">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="39af2-195">사용자가 이름 또는 성을 입력하면 JavaScript에서는 원격 호출을 실행하여 해당 이름 쌍이 사용 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-195">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="39af2-196">두 개 이상의 추가 필드에 대한 유효성을 검사하려면 해당 필드를 쉼표로 구분된 목록으로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-196">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="39af2-197">예를 들어 `MiddleName` 속성을 모델에 추가하려면 `[Remote]` 특성을 다음 예제와 같이 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-197">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="39af2-198">모든 특성 인수와 같은 `AdditionalFields`은 상수 식이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-198">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="39af2-199">따라서 `AdditionalFields`를 초기화하기 위해 [보간된 문자열](/dotnet/csharp/language-reference/keywords/interpolated-strings)을 사용하거나 <xref:System.String.Join*>을 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="39af2-199">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="39af2-200">기본 제공 특성에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="39af2-200">Alternatives to built-in attributes</span></span>

<span data-ttu-id="39af2-201">기본 제공 특성이 제공하지 않는 유효성 검사가 필요한 경우 다음과 같이 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-201">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="39af2-202">[사용자 지정 특성을 만듭니다](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="39af2-202">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="39af2-203">[IValidatableObject](#ivalidatableobject)를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-203">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="39af2-204">사용자 지정 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-204">Custom attributes</span></span>

<span data-ttu-id="39af2-205">기본 제공 유효성 검사 특성이 처리하지 않는 시나리오의 경우 사용자 지정 유효성 검사 특성을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-205">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="39af2-206"><xref:System.ComponentModel.DataAnnotations.ValidationAttribute>에서 상속하는 클래스를 만들고 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-206">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="39af2-207">`IsValid` 메서드에 대해서는 유효성을 검사할 입력인 *값*이라는 개체를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-207">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="39af2-208">또한 오버로드에 대해서는 모델 바인딩에 의해 생성된 모델 인스턴스와 같은 추가 정보를 제공하는 `ValidationContext` 개체를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-208">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="39af2-209">다음 예제에서는 *클래식* 장르의 영화에 대한 개봉 날짜가 지정된 연도보다 이후가 아닌지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-209">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="39af2-210">`[ClassicMovie]` 특성:</span><span class="sxs-lookup"><span data-stu-id="39af2-210">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="39af2-211">서버에서만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-211">Is only run on the server.</span></span>
* <span data-ttu-id="39af2-212">클래식 동영상의 경우 릴리스 날짜의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-212">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="39af2-213">위의 예제에서 `movie` 변수는 양식 제출에서 제공된 데이터를 포함하는 `Movie` 개체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-213">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="39af2-214">유효성 검사에 실패하면 오류 메시지가 발생하여 `ValidationResult`가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-214">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="39af2-215">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="39af2-215">IValidatableObject</span></span>

<span data-ttu-id="39af2-216">앞의 예제는 `Movie` 형식에 대해서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-216">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="39af2-217">또 다른 클래스 수준 유효성 검사 방법은 다음 예제와 같이 모델 클래스 내에 `IValidatableObject`를 구현하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-217">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="39af2-218">최상위 노드 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-218">Top-level node validation</span></span>

<span data-ttu-id="39af2-219">최상위 수준 노드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-219">Top-level nodes include:</span></span>

* <span data-ttu-id="39af2-220">작업 매개 변수</span><span class="sxs-lookup"><span data-stu-id="39af2-220">Action parameters</span></span>
* <span data-ttu-id="39af2-221">컨트롤러 속성</span><span class="sxs-lookup"><span data-stu-id="39af2-221">Controller properties</span></span>
* <span data-ttu-id="39af2-222">페이지 처리기 매개 변수</span><span class="sxs-lookup"><span data-stu-id="39af2-222">Page handler parameters</span></span>
* <span data-ttu-id="39af2-223">페이지 모델 속성</span><span class="sxs-lookup"><span data-stu-id="39af2-223">Page model properties</span></span>

<span data-ttu-id="39af2-224">모델 바인딩 최상위 노드는 모델 속성의 유효성을 검사하는 것 외에도 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-224">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="39af2-225">샘플 앱의 다음 예제에서 `VerifyPhone` 메서드는 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>를 사용하여 `phone` 작업 매개 변수의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-225">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="39af2-226">최상위 노드는 유효성 검사 특성과 함께 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-226">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="39af2-227">샘플 앱의 다음 예제에서 `CheckAge` 메서드는 양식을 제출할 때 쿼리 문자열에서 `age` 매개 변수를 바인딩해야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-227">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="39af2-228">기간 확인 페이지(*CheckAge.cshtml*)에는 두 가지 양식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-228">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="39af2-229">첫 번째 양식은 `99`의 `Age` 값을 쿼리 문자열 매개 변수(`https://localhost:5001/Users/CheckAge?Age=99`)로 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-229">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="39af2-230">쿼리 문자열에서 올바른 서식이 지정된 `age` 매개 변수를 제출하면 양식이 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-230">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="39af2-231">기간 확인 페이지의 두 번째 양식은 요청 본문의 `Age` 값을 제출하고 유효성 검사가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-231">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="39af2-232">`age` 매개 변수는 쿼리 문자열에서 가져와야 하므로 바인딩이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-232">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="39af2-233">최대 오류 수</span><span class="sxs-lookup"><span data-stu-id="39af2-233">Maximum errors</span></span>

<span data-ttu-id="39af2-234">유효성 검사는 최대 오류 수(기본값 200)에 도달하면 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="39af2-235">`Startup.ConfigureServices`에 다음 코드로 이 번호를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="39af2-236">최대 재귀</span><span class="sxs-lookup"><span data-stu-id="39af2-236">Maximum recursion</span></span>

<span data-ttu-id="39af2-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>는 유효성 검사 중인 모델의 개체 그래프를 트래버스합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="39af2-238">깊거나 무한히 재귀하는 모델의 경우 유효성 검사를 실행하면 스택 오버플로가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-238">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="39af2-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth)는 방문자 재귀가 구성된 깊이를 초과하는 경우 유효성 검사를 조기에 중지하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="39af2-240">`MvcOptions.MaxValidationDepth`의 기본값은 32입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-240">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="39af2-241">자동 단락</span><span class="sxs-lookup"><span data-stu-id="39af2-241">Automatic short-circuit</span></span>

<span data-ttu-id="39af2-242">모델 그래프에 대한 유효성 검사가 필요하지 않은 경우 유효성 검사는 자동으로 단락됩니다(건너뜀).</span><span class="sxs-lookup"><span data-stu-id="39af2-242">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="39af2-243">런타임에 유효성 검사를 건너뛰는 개체로는 기본 형식(`byte[]`, `string[]`, `Dictionary<string, string>`) 수집 및 유효성 검사기를 포함하지 않은 복합 개체 그래프가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-243">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="39af2-244">유효성 검사를 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="39af2-244">Disable validation</span></span>

<span data-ttu-id="39af2-245">유효성 검사를 사용하지 않도록 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-245">To disable validation:</span></span>

1. <span data-ttu-id="39af2-246">필드를 잘못된 것으로 표시하지 않는 `IObjectModelValidator` 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-246">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="39af2-247">종속성 검사 컨테이너에서 다음 코드를 `Startup.ConfigureServices`에 추가하여 기본 `IObjectModelValidator` 구현을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-247">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="39af2-248">모델 바인딩에서 발생하는 모델 상태 오류는 여전히 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-248">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="39af2-249">클라이언트 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-249">Client-side validation</span></span>

<span data-ttu-id="39af2-250">클라이언트 쪽 유효성 검사는 형식이 유효할 때까지 전송을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-250">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="39af2-251">제출 단추는 형식을 전송하거나 오류 메시지를 표시하는 JavaScript를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-251">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="39af2-252">클라이언트 쪽 유효성 검사는 양식에 대한 입력 오류가 있는 경우 불필요한 서버 왕복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-252">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="39af2-253">*_Layout.cshtml* 및 *_ValidationScriptsPartial.cshtml*의 다음 스크립트 참조는 클라이언트 쪽 유효성 검사를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-253">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="39af2-254">[jQuery 비간섭 유효성 검사](https://github.com/aspnet/jquery-validation-unobtrusive) 스크립트는 널리 사용되는 [jQuery 유효성 검사](https://jqueryvalidation.org/) 플러그 인에 기반한 사용자 지정 Microsoft 프런트 엔드 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-254">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="39af2-255">jQuery 비간섭 유효성 검사를 사용하지 않을 경우 두 위치(모델 속성에 대한 서버 쪽 유효성 검사 특성에서 한 번 및 클라이언트 쪽 스크립트에서 다시 한 번)에서 동일한 유효성 검사 논리를 코딩해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-255">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="39af2-256">그 대신 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미 ](xref:mvc/views/overview)는 유효성 검사가 필요한 양식 요소에 대해 모델 특성의 유효성 검사 특성 및 형식 메타데이터를 사용하여 HTML 5 `data-` 특성을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-256">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="39af2-257">jQuery 비간섭 유효성 검사는 `data-` 특성을 구문 분석하고 jQuery 유효성 검사에 대한 논리를 전달하여 효과적으로 서버 쪽 유효성 검사 논리를 클라이언트에 “복사”합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-257">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="39af2-258">다음과 같이 태그 도우미를 사용하여 클라이언트에서 유효성 검사 오류를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-258">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="39af2-259">위의 태그 도우미는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-259">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="39af2-260">HTML의 `data-` 특성 출력은 `Movie.ReleaseDate` 속성에 대한 유효성 검사 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-260">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="39af2-261">`data-val-required` 특성은 사용자가 릴리스 날짜 필드를 입력하지 않았음을 표시하는 오류 메시지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-261">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="39af2-262">jQuery 비간섭 유효성 검사는 jQuery 유효성 검사 [required()](https://jqueryvalidation.org/required-method/) 메서드에 이 값을 전달합니다. 그러면 **\<span>** 요소와 함께 해당 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-262">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="39af2-263">데이터 형식 유효성 검사는 `[DataType]` 특성에 의해 재정의되지 않은 한, 속성의 .NET 형식을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-263">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="39af2-264">브라우저는 고유한 기본 오류 메시지를 가지고 있지만 jQuery유효성 검사의 비간섭 유효성 검사 패키지는 해당 메시지를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-264">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="39af2-265">`[DataType]` 특성 및 `[EmailAddress]`와 같은 서브클래스를 사용하여 오류 메시지를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-265">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="39af2-266">비간섭 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-266">Unobtrusive validation</span></span>

<span data-ttu-id="39af2-267">비간섭 유효성 검사에 대한 자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/1111)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-267">For information on unobtrusive validation, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="39af2-268">동적 형식에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="39af2-268">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="39af2-269">jQuery 비간섭 유효성 검사는 페이지가 먼저 로드되는 경우 유효성 검사 논리 및 매개 변수를 jQuery 유효성 검사에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-269">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="39af2-270">따라서 동적으로 생성된 양식에 대해서는 유효성 검사가 자동으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-270">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="39af2-271">유효성 검사를 사용하도록 설정하려면 jQuery 비간섭 유효성 검사에서 동적 폼을 만든 후에 즉시 구문 분석하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-271">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="39af2-272">예를 들어 다음 코드에서는 AJAX를 통해 추가된 양식에 대해 클라이언트 쪽 유효성 검사를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-272">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="39af2-273">`$.validator.unobtrusive.parse()` 메서드는 하나의 인수에 대해 jQuery 선택기를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-273">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="39af2-274">이 메서드는 jQuery 비간섭 유효성 검사에서 해당 선택기 내에 있는 형식의 `data-` 특성을 구문 분석하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-274">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="39af2-275">그러면 이러한 특성의 값이 jQuery 유효성 검사 플러그 인에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-275">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="39af2-276">동적 컨트롤에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="39af2-276">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="39af2-277">`$.validator.unobtrusive.parse()` 메서드는 `<input>` 및 `<select/>`와 같은 동적으로 생성된 개별 컨트롤이 아닌 전체 양식에 대해 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-277">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="39af2-278">양식을 다시 구문 분석하려면 다음 예제와 같이 이전에 양식을 구문 분석했을 때 추가된 유효성 검사 데이터를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-278">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="39af2-279">사용자 지정 클라이언트 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-279">Custom client-side validation</span></span>

<span data-ttu-id="39af2-280">사용자 지정 클라이언트 쪽 유효성 검사는 사용자 지정 jQuery 유효성 검사 어댑터에서 작동하는 `data-` HTML 특성을 생성하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-280">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="39af2-281">다음 샘플 어댑터 코드는 이 문서에서 이전에 소개한 `[ClassicMovie]` 및 `[ClassicMovieWithClientValidator]` 특성용으로 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-281">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="39af2-282">어댑터 작성 방법에 대한 자세한 내용은 [jQuery 유효성 검사 설명서](https://jqueryvalidation.org/documentation/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-282">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="39af2-283">지정된 필드에 대한 어댑터 사용은 `data-` 특성에 의해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-283">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="39af2-284">필드를 유효성 검사가 적용되는 것으로 표시합니다(`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="39af2-284">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="39af2-285">유효성 검사 규칙 이름 및 오류 메시지 텍스트(예: `data-val-rulename="Error message."`)를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-285">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="39af2-286">유효섬 검사기에 필요한 추가 매개 변수(예: `data-val-rulename-param1="value"`)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-286">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="39af2-287">다음 에제에서는 샘플 앱의 `ClassicMovie` 특성에 대한 `data-` 특성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-287">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="39af2-288">앞에서 설명했듯이 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미](xref:mvc/views/overview)는 유효성 검사 특성의 정보를 사용하여 `data-` 특성을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-288">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="39af2-289">사용자 지정 `data-` HTML 특성을 생성하는 코드를 작성하는 두 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-289">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="39af2-290">`AttributeAdapterBase<TAttribute>`에서 파생하는 클래스 및 `IValidationAttributeAdapterProvider`를 구현하는 클래스를 만들고 사용자의 특성 및 해당 어댑터를 DI에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-290">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="39af2-291">이 방법은 서버 관련 및 클라이언트 관련 유효성 검사 코드가 별도의 클래스에 있는 [단일 책임 보안 주체](https://wikipedia.org/wiki/Single_responsibility_principle)를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-291">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="39af2-292">또한 이 어댑터는 DI에 등록되었으므로 필요한 경우 DI의 다른 서비스를 사용할 수 있다는 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-292">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="39af2-293">사용자의 `ValidationAttribute` 클래스에서 `IClientModelValidator`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-293">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="39af2-294">이 방법은 특성이 서버 쪽 유효성 검사를 수행하지 않고 DI의 서비스가 필요하지 않은 경우 적절할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-294">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="39af2-295">클라이언트 쪽 유효성 검사에 대한 특성 어댑터</span><span class="sxs-lookup"><span data-stu-id="39af2-295">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="39af2-296">이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovie` 특성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-296">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="39af2-297">이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-297">To add client validation by using this method:</span></span>

1. <span data-ttu-id="39af2-298">사용자 지정 유효성 검사 특성에 대한 특성 어댑터 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-298">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="39af2-299">[AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)에서 클래스를 파생합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-299">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="39af2-300">이 예제와 같이 렌더링된 출력에 `data-` 특성을 추가하는 `AddValidation` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-300">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="39af2-301"><xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>를 구현하는 어댑터 공급자 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-301">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="39af2-302">`GetAttributeAdapter` 메서드에서 다음 예제와 같이 사용자 지정 특성을 어댑터의 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-302">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="39af2-303">DI용 어댑터 공급자를 `Startup.ConfigureServices`에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-303">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="39af2-304">클라이언트 쪽 유효성 검사를 위한 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="39af2-304">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="39af2-305">이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovieWithClientValidator` 특성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-305">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="39af2-306">이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-306">To add client validation by using this method:</span></span>

* <span data-ttu-id="39af2-307">사용자 지정 유효성 검사 특성에서 `IClientModelValidator` 인터페이스를 구현하고 `AddValidation` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-307">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="39af2-308">`AddValidation` 메서드에서 다음 예제와 같이 유효성 검사를 위한 `data-` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-308">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="39af2-309">클라이언트 쪽 유효성 검사를 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="39af2-309">Disable client-side validation</span></span>

<span data-ttu-id="39af2-310">다음 코드는 Razor Pages에 클라이언트 유효성 검사를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-310">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="39af2-311">클라이언트 쪽 유효성 검사를 사용하지 않도록 설정하는 다른 옵션:</span><span class="sxs-lookup"><span data-stu-id="39af2-311">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="39af2-312">모든 *.cshtml* 파일의 `_ValidationScriptsPartial`에 대한 참조를 주석으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-312">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="39af2-313">*Pages\Shared\_ValidationScriptsPartial* 파일의 콘텐츠를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-313">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="39af2-314">위의 방법으로는 ASP.NET Core ID Razor 클래스 라이브러리의 클라이언트 쪽 유효성 검사를 방지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-314">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="39af2-315">자세한 내용은 <xref:security/authentication/scaffold-identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-315">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39af2-316">추가 자료</span><span class="sxs-lookup"><span data-stu-id="39af2-316">Additional resources</span></span>

* [<span data-ttu-id="39af2-317">System.ComponentModel.DataAnnotations 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="39af2-317">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="39af2-318">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="39af2-318">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39af2-319">이 문서에서는 ASP.NET Core MVC 또는 Razor Pages 앱에서 사용자 입력의 유효성을 검사하는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-319">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="39af2-320">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-320">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="39af2-321">모델 상태</span><span class="sxs-lookup"><span data-stu-id="39af2-321">Model state</span></span>

<span data-ttu-id="39af2-322">모델 상태는 모델 바인딩 및 모델 유효성 검사 두 하위 시스템에서 발생하는 오류를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-322">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="39af2-323">[모델 바인딩](model-binding.md)에서 발생하는 오류는 일반적으로 데이터 변환 오류입니다(예를 들어 정수를 입력해야 하는 필드에 "x"를 입력하는 오류).</span><span class="sxs-lookup"><span data-stu-id="39af2-323">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="39af2-324">모델 유효성 검사는 모델 바인딩 후에 실행되며 데이터가 비즈니스 규칙을 준수하지 않는 경우(예를 들어 1에서 5 사이의등급을 입력해야 하는 필드에 0을 입력) 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-324">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="39af2-325">모델 바인딩과 유효성 검사 모두 컨트롤러 작업 또는 Razor Pages 처리기 메서드를 실행하기 전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-325">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="39af2-326">웹앱의 경우 `ModelState.IsValid`를 검사하고 적절히 반응하는 기능은 앱에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-326">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="39af2-327">일반적으로 웹앱은 페이지를 오류 메시지와 함께 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-327">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="39af2-328">웹 API 컨트롤러는 `[ApiController]` 특성을 포함하는 경우 `ModelState.IsValid`를 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-328">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="39af2-329">이 경우 모델 상태가 잘못되면 오류 세부 정보를 포함하는 자동 HTTP 400 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-329">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="39af2-330">자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-330">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="39af2-331">유효성 검사 다시 실행</span><span class="sxs-lookup"><span data-stu-id="39af2-331">Rerun validation</span></span>

<span data-ttu-id="39af2-332">유효성 검사는 자동으로 실행 되지만 수동으로 반복하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-332">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="39af2-333">예를 들어 속성에 대한 값을 계산하고 해당 속성을 계산된 값으로 설정한 후 유효성 검사를 다시 실행하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-333">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="39af2-334">유효성 검사를 다시 실행하려면 다음과 같이 `TryValidateModel` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-334">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="39af2-335">유효성 검사 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-335">Validation attributes</span></span>

<span data-ttu-id="39af2-336">유효성 검사 특성을 사용하여 모델 속성에 대한 유효성 검사 규칙을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-336">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="39af2-337">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)의 다음 예제는 유효성 검사 특성으로 주석을 단 모델 클래스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-337">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="39af2-338">`[ClassicMovie]` 특성은 사용자 지정 유효성 검사 특성이며 다른 특성은 기본 제공 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-338">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="39af2-339">사용자 지정 특성을 구현하는 다른 방법을 보여 주는 `[ClassicMovie2]`는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-339">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="39af2-340">기본 제공 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-340">Built-in attributes</span></span>

<span data-ttu-id="39af2-341">기본 제공 유효성 검사 특성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-341">Built-in validation attributes include:</span></span>

* <span data-ttu-id="39af2-342">`[CreditCard]`: 속성에 신용 카드 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-342">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="39af2-343">`[Compare]`: 모델의 두 속성이 일치하는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-343">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="39af2-344">예를 들어 *Register.cshtml.cs* 파일은 `[Compare]`를 사용하여 입력된 두 암호가 일치하는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-344">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="39af2-345">[스캐폴드 ID](xref:security/authentication/scaffold-identity)는 등록 코드를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-345">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="39af2-346">`[EmailAddress]`: 속성에 이메일 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-346">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="39af2-347">`[Phone]`: 속성에 전화 번호 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-347">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="39af2-348">`[Range]`: 속성 값이 지정된 범위 내에 포함되는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-348">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="39af2-349">`[RegularExpression]`: 속성 값이 지정된 정규 식과 일치하는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-349">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="39af2-350">`[Required]`: 필드가 Null이 아닌지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-350">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="39af2-351">이 특성의 동작에 대한 자세한 내용은 [`[Required]` 특성](#required-attribute)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-351">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="39af2-352">`[StringLength]`: 문자열 속성 값이 지정된 길이 한계를 초과하지 않는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-352">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="39af2-353">`[Url]`: 속성에 URL 형식이 있는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-353">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="39af2-354">`[Remote]`: 서버에 대한 작업 명령을 호출하여 클라이언트에 대한 입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-354">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="39af2-355">이 특성의 동작에 대한 자세한 내용은 [`[Remote]` 특성](#remote-attribute)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-355">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="39af2-356">클라이언트 쪽 유효성 검사에 `[RegularExpression]` 특성을 사용하면 클라이언트의 JavaScript에서 regex가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-356">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="39af2-357">이는 [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) 일치 동작이 사용됨을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-357">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="39af2-358">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/corefx/issues/42487)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-358">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="39af2-359">유효성 검사 특성의 전체 목록은 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 네임스페이스에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-359">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="39af2-360">오류 메시지</span><span class="sxs-lookup"><span data-stu-id="39af2-360">Error messages</span></span>

<span data-ttu-id="39af2-361">유효성 검사 특성을 사용하여 잘못된 입력에 대해 표시할 오류 메시지를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-361">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="39af2-362">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="39af2-362">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="39af2-363">내부적으로 이 특성은 필드 이름에 대한 자리 표시자 및 경우에 따라 추가 자리 표시자를 사용하여 `String.Format`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-363">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="39af2-364">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="39af2-364">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="39af2-365">`Name` 속성에 적용할 경우 위의 코드에 의해 생성되는 오류 메시지는 "이름의 길이는 6문자에서 8문자 사이여야 합니다"일 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-365">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="39af2-366">특정 특성의 오류 메시지에 대해 `String.Format` 에 전달되는 매개 변수를 알아보려면 [DataAnnotations 소스 코드](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-366">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="39af2-367">[Required] 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-367">[Required] attribute</span></span>

<span data-ttu-id="39af2-368">기본적으로 유효성 검사 시스템은 Null을 허용하지 않는 매개 변수 또는 속성을 `[Required]` 특성을 포함한 것처럼 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-368">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="39af2-369">`decimal` 및 `int`와 같은 [값 형식](/dotnet/csharp/language-reference/keywords/value-types)은 Null을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-369">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="39af2-370">서버에 대한 [Required] 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-370">[Required] validation on the server</span></span>

<span data-ttu-id="39af2-371">서버에서 속성이 Null인 경우 필수 값이 없는 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-371">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="39af2-372">Null을 허용하지 않는 필드는 언제나 유효하며 [Required] 특성의 오류 메시지는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-372">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="39af2-373">그러나 Null을 허용하지 않는 속성에 대한 모델 바인딩이 실패하여 `The value '' is invalid`와 같은 오류 메시지가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-373">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="39af2-374">Null을 허용하지 않는 형식의 서버 쪽 유효성 검사에 대한 사용자 지정 오류 메시지를 지정하려는 경우 다음과 같은 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-374">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="39af2-375">필드를 Null 허용으로 만듭니다(예: `decimal` 대신에 `decimal?`).</span><span class="sxs-lookup"><span data-stu-id="39af2-375">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="39af2-376">[Null을 허용하는 \<T>](/dotnet/csharp/programming-guide/nullable-types/) 값 형식은 표준 Nul을 허용 형식처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-376">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="39af2-377">다음 예제에서와 같이 모델 바인딩에 의해 사용할 기본 오류 메시지를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-377">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="39af2-378">기본 메시지를 설정할 수 있는 모델 바인딩 오류에 대한 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="39af2-378">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="39af2-379">클라이언트에 대한 [Required] 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-379">[Required] validation on the client</span></span>

<span data-ttu-id="39af2-380">클라이언트에서 Null을 허용하지 않는 형식 및 문자열은 서버의 경우와 다르게 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-380">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="39af2-381">클라이언트에서:</span><span class="sxs-lookup"><span data-stu-id="39af2-381">On the client:</span></span>

* <span data-ttu-id="39af2-382">값은 해당 값에 대한 입력이 있는 경우에만 존재하는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-382">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="39af2-383">그러므로 클라이언트 쪽 유효성 검사에서는 Null을 허용하지 않는 형식을 Null 허용 형식과 동일하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-383">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="39af2-384">문자열 필드의 공백은 jQuery 유효성 검사 [필수](https://jqueryvalidation.org/required-method/) 메서드에 의해 유효한 입력으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-384">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="39af2-385">서버 쪽 유효성 검사에서는 공백만 입력된 경우 필수 문자열 필드를 잘못된 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-385">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="39af2-386">위에서 설명했듯이 Null을 허용하지 않는 형식은 `[Required]` 특성을 포함한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-386">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="39af2-387">즉, `[Required]` 특성을 적용하지 않더라도 클라이언트 쪽 유효성 검사가 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-387">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="39af2-388">그러나 해당 특성을 사용하지 않으면 기본 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-388">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="39af2-389">사용자 지정 오류 메시지를 지정하려면 해당 특성을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="39af2-389">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="39af2-390">[Remote] 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-390">[Remote] attribute</span></span>

<span data-ttu-id="39af2-391">`[Remote]` 특성은 필드 입력이 유효한지 여부를 결정하기 위해 서버에서 메서드를 호출해야 하는 클라이언트 쪽 유효성 검사를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-391">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="39af2-392">예를 들어 앱에서 사용자 이름을 이미 사용 중인지 여부를 확인해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-392">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="39af2-393">원격 유효성 검사를 구현하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-393">To implement remote validation:</span></span>

1. <span data-ttu-id="39af2-394">JavaScript가 호출할 작업 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-394">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="39af2-395">jQuery 유효성 검사 [remote](https://jqueryvalidation.org/remote-method/) 메서드는 JSON 응답을 수신해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-395">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="39af2-396">`"true"`는 입력 데이터가 유효함을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-396">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="39af2-397">`"false"`, `undefined` 또는 `null`은 입력이 잘못되었음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-397">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="39af2-398">기본 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-398">Display the default error message.</span></span>
   * <span data-ttu-id="39af2-399">다른 문자열은 모두 입력이 잘못되었음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-399">Any other string means the input is invalid.</span></span> <span data-ttu-id="39af2-400">문자열을 사용자 지정 오류 메시지로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-400">Display the string as a custom error message.</span></span>

   <span data-ttu-id="39af2-401">다음은 사용자 지정 오류 메시지를 반환하는 작업 메서드의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-401">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="39af2-402">다음 예제와 같이 모델 클래스에서는 유효성 검사 작업 메서드를 가리키는 `[Remote]` 특성으로 속성에 주석을 답니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-402">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="39af2-403">`Microsoft.AspNetCore.Mvc` 네임스페이스에 `[Remote]` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-403">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="39af2-404">`Microsoft.AspNetCore.App` 또는 `Microsoft.AspNetCore.All` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-404">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="39af2-405">추가 필드</span><span class="sxs-lookup"><span data-stu-id="39af2-405">Additional fields</span></span>

<span data-ttu-id="39af2-406">`[Remote]` 특성의 `AdditionalFields` 속성을 사용하여 서버의 데이터를 기준으로 필드 조합의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-406">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="39af2-407">예를 들어 `User` 모델이 `FirstName` 및 `LastName` 속성을 포함한 경우 해당 이름 쌍을 이미 가진 기존 사용자가 없는지 확인하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-407">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="39af2-408">다음 예제에서는 `AdditionalFields`을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-408">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="39af2-409">`AdditionalFields`는 명시적으로 문자열 `"FirstName"` 및 `"LastName"`으로 설정할 수 있지만 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 연산자를 사용하면 이후 리팩터링이 간단해집니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-409">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="39af2-410">이 유효성 검사에 대한 작업 메서드에 대해서는 이름과 성 인수를 모두 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-410">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="39af2-411">사용자가 이름 또는 성을 입력하면 JavaScript에서는 원격 호출을 실행하여 해당 이름 쌍이 사용 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-411">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="39af2-412">두 개 이상의 추가 필드에 대한 유효성을 검사하려면 해당 필드를 쉼표로 구분된 목록으로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-412">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="39af2-413">예를 들어 `MiddleName` 속성을 모델에 추가하려면 `[Remote]` 특성을 다음 예제와 같이 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-413">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="39af2-414">모든 특성 인수와 같은 `AdditionalFields`은 상수 식이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-414">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="39af2-415">따라서 `AdditionalFields`를 초기화하기 위해 [보간된 문자열](/dotnet/csharp/language-reference/keywords/interpolated-strings)을 사용하거나 <xref:System.String.Join*>을 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="39af2-415">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="39af2-416">기본 제공 특성에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="39af2-416">Alternatives to built-in attributes</span></span>

<span data-ttu-id="39af2-417">기본 제공 특성이 제공하지 않는 유효성 검사가 필요한 경우 다음과 같이 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-417">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="39af2-418">[사용자 지정 특성을 만듭니다](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="39af2-418">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="39af2-419">[IValidatableObject](#ivalidatableobject)를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-419">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="39af2-420">사용자 지정 특성</span><span class="sxs-lookup"><span data-stu-id="39af2-420">Custom attributes</span></span>

<span data-ttu-id="39af2-421">기본 제공 유효성 검사 특성이 처리하지 않는 시나리오의 경우 사용자 지정 유효성 검사 특성을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-421">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="39af2-422"><xref:System.ComponentModel.DataAnnotations.ValidationAttribute>에서 상속하는 클래스를 만들고 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-422">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="39af2-423">`IsValid` 메서드에 대해서는 유효성을 검사할 입력인 *값*이라는 개체를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-423">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="39af2-424">또한 오버로드에 대해서는 모델 바인딩에 의해 생성된 모델 인스턴스와 같은 추가 정보를 제공하는 `ValidationContext` 개체를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-424">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="39af2-425">다음 예제에서는 *클래식* 장르의 영화에 대한 개봉 날짜가 지정된 연도보다 이후가 아닌지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-425">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="39af2-426">`[ClassicMovie2]` 특성은 먼저 장르를 확인하고 해당 장르가 *클래식*인 경우에만 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-426">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="39af2-427">클래식으로 식별된 영화에 대해서는 개봉 날짜를 확인하여 해당 날짜가 특성 생성자에 전달된 한계보다 이후가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-427">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="39af2-428">위의 예제에서 `movie` 변수는 양식 제출에서 제공된 데이터를 포함하는 `Movie` 개체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-428">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="39af2-429">`IsValid` 메서드는 날짜 및 장르를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-429">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="39af2-430">유효성 검사에 성공하면 `IsValid`는 `ValidationResult.Success` 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-430">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="39af2-431">유효성 검사에 실패하면 오류 메시지가 발생하여 `ValidationResult`가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-431">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="39af2-432">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="39af2-432">IValidatableObject</span></span>

<span data-ttu-id="39af2-433">앞의 예제는 `Movie` 형식에 대해서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-433">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="39af2-434">또 다른 클래스 수준 유효성 검사 방법은 다음 예제와 같이 모델 클래스 내에 `IValidatableObject`를 구현하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-434">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="39af2-435">최상위 노드 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-435">Top-level node validation</span></span>

<span data-ttu-id="39af2-436">최상위 수준 노드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-436">Top-level nodes include:</span></span>

* <span data-ttu-id="39af2-437">작업 매개 변수</span><span class="sxs-lookup"><span data-stu-id="39af2-437">Action parameters</span></span>
* <span data-ttu-id="39af2-438">컨트롤러 속성</span><span class="sxs-lookup"><span data-stu-id="39af2-438">Controller properties</span></span>
* <span data-ttu-id="39af2-439">페이지 처리기 매개 변수</span><span class="sxs-lookup"><span data-stu-id="39af2-439">Page handler parameters</span></span>
* <span data-ttu-id="39af2-440">페이지 모델 속성</span><span class="sxs-lookup"><span data-stu-id="39af2-440">Page model properties</span></span>

<span data-ttu-id="39af2-441">모델 바인딩 최상위 노드는 모델 속성의 유효성을 검사하는 것 외에도 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-441">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="39af2-442">샘플 앱의 다음 예제에서 `VerifyPhone` 메서드는 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>를 사용하여 `phone` 작업 매개 변수의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-442">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="39af2-443">최상위 노드는 유효성 검사 특성과 함께 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-443">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="39af2-444">샘플 앱의 다음 예제에서 `CheckAge` 메서드는 양식을 제출할 때 쿼리 문자열에서 `age` 매개 변수를 바인딩해야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-444">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="39af2-445">기간 확인 페이지(*CheckAge.cshtml*)에는 두 가지 양식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-445">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="39af2-446">첫 번째 양식은 `99`의 `Age` 값을 쿼리 문자열(`https://localhost:5001/Users/CheckAge?Age=99`)로 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-446">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="39af2-447">쿼리 문자열에서 올바른 서식이 지정된 `age` 매개 변수를 제출하면 양식이 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-447">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="39af2-448">기간 확인 페이지의 두 번째 양식은 요청 본문의 `Age` 값을 제출하고 유효성 검사가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-448">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="39af2-449">`age` 매개 변수는 쿼리 문자열에서 가져와야 하므로 바인딩이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-449">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="39af2-450">`CompatibilityVersion.Version_2_1` 이상을 사용하여 실행하는 경우 최상위 노드 유효성 검사가 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-450">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="39af2-451">그렇지 않으면 최상위 노드 유효성 검사가 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-451">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="39af2-452">아래와 같이 (`Startup.ConfigureServices`)에서 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> 속성을 설정하여 기본 옵션을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-452">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="39af2-453">최대 오류 수</span><span class="sxs-lookup"><span data-stu-id="39af2-453">Maximum errors</span></span>

<span data-ttu-id="39af2-454">유효성 검사는 최대 오류 수(기본값 200)에 도달하면 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-454">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="39af2-455">`Startup.ConfigureServices`에 다음 코드로 이 번호를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-455">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="39af2-456">최대 재귀</span><span class="sxs-lookup"><span data-stu-id="39af2-456">Maximum recursion</span></span>

<span data-ttu-id="39af2-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>는 유효성 검사 중인 모델의 개체 그래프를 트래버스합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="39af2-458">매우 깊거나 무한히 재귀하는 모델의 경우 유효성 검사를 실행하면 스택 오버플로가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-458">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="39af2-459">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth)는 방문자 재귀가 구성된 깊이를 초과하는 경우 유효성 검사를 조기에 중지하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-459">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="39af2-460">`CompatibilityVersion.Version_2_2` 이상에서 실행하는 경우 `MvcOptions.MaxValidationDepth`의 기본값은 32입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-460">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="39af2-461">그보다 이전 버전의 경우 이 값은 Null이며, 이는 깊이 제약 조건이 없음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-461">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="39af2-462">자동 단락</span><span class="sxs-lookup"><span data-stu-id="39af2-462">Automatic short-circuit</span></span>

<span data-ttu-id="39af2-463">모델 그래프에 대한 유효성 검사가 필요하지 않은 경우 유효성 검사는 자동으로 단락됩니다(건너뜀).</span><span class="sxs-lookup"><span data-stu-id="39af2-463">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="39af2-464">런타임에 유효성 검사를 건너뛰는 개체로는 기본 형식(`byte[]`, `string[]`, `Dictionary<string, string>`) 수집 및 유효성 검사기를 포함하지 않은 복합 개체 그래프가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-464">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="39af2-465">유효성 검사를 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="39af2-465">Disable validation</span></span>

<span data-ttu-id="39af2-466">유효성 검사를 사용하지 않도록 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-466">To disable validation:</span></span>

1. <span data-ttu-id="39af2-467">필드를 잘못된 것으로 표시하지 않는 `IObjectModelValidator` 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-467">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="39af2-468">종속성 검사 컨테이너에서 다음 코드를 `Startup.ConfigureServices`에 추가하여 기본 `IObjectModelValidator` 구현을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-468">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="39af2-469">모델 바인딩에서 발생하는 모델 상태 오류는 여전히 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-469">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="39af2-470">클라이언트 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-470">Client-side validation</span></span>

<span data-ttu-id="39af2-471">클라이언트 쪽 유효성 검사는 형식이 유효할 때까지 전송을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-471">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="39af2-472">제출 단추는 형식을 전송하거나 오류 메시지를 표시하는 JavaScript를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-472">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="39af2-473">클라이언트 쪽 유효성 검사는 양식에 대한 입력 오류가 있는 경우 불필요한 서버 왕복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-473">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="39af2-474">*_Layout.cshtml* 및 *_ValidationScriptsPartial.cshtml*의 다음 스크립트 참조는 클라이언트 쪽 유효성 검사를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-474">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="39af2-475">[jQuery 비간섭 유효성 검사](https://github.com/aspnet/jquery-validation-unobtrusive) 스크립트는 널리 사용되는 [jQuery 유효성 검사](https://jqueryvalidation.org/) 플러그 인에 기반한 사용자 지정 Microsoft 프런트 엔드 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-475">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="39af2-476">jQuery 비간섭 유효성 검사를 사용하지 않을 경우 두 위치(모델 속성에 대한 서버 쪽 유효성 검사 특성에서 한 번 및 클라이언트 쪽 스크립트에서 다시 한 번)에서 동일한 유효성 검사 논리를 코딩해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-476">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="39af2-477">그 대신 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미 ](xref:mvc/views/overview)는 유효성 검사가 필요한 양식 요소에 대해 모델 특성의 유효성 검사 특성 및 형식 메타데이터를 사용하여 HTML 5 `data-` 특성을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-477">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="39af2-478">jQuery 비간섭 유효성 검사는 `data-` 특성을 구문 분석하고 jQuery 유효성 검사에 대한 논리를 전달하여 효과적으로 서버 쪽 유효성 검사 논리를 클라이언트에 “복사”합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-478">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="39af2-479">다음과 같이 태그 도우미를 사용하여 클라이언트에서 유효성 검사 오류를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-479">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="39af2-480">위의 태그 도우미는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-480">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="39af2-481">HTML의 `data-` 특성 출력은 `ReleaseDate` 속성에 대한 유효성 검사 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-481">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="39af2-482">`data-val-required` 특성은 사용자가 릴리스 날짜 필드를 입력하지 않았음을 표시하는 오류 메시지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-482">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="39af2-483">jQuery 비간섭 유효성 검사는 jQuery 유효성 검사 [required()](https://jqueryvalidation.org/required-method/) 메서드에 이 값을 전달합니다. 그러면 **\<span>** 요소와 함께 해당 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-483">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="39af2-484">데이터 형식 유효성 검사는 `[DataType]` 특성에 의해 재정의되지 않은 한, 속성의 .NET 형식을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-484">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="39af2-485">브라우저는 고유한 기본 오류 메시지를 가지고 있지만 jQuery유효성 검사의 비간섭 유효성 검사 패키지는 해당 메시지를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-485">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="39af2-486">`[DataType]` 특성 및 `[EmailAddress]`와 같은 서브클래스를 사용하여 오류 메시지를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-486">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="39af2-487">동적 형식에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="39af2-487">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="39af2-488">jQuery 비간섭 유효성 검사는 페이지가 먼저 로드되는 경우 유효성 검사 논리 및 매개 변수를 jQuery 유효성 검사에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-488">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="39af2-489">따라서 동적으로 생성된 양식에 대해서는 유효성 검사가 자동으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-489">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="39af2-490">유효성 검사를 사용하도록 설정하려면 jQuery 비간섭 유효성 검사에서 동적 폼을 만든 후에 즉시 구문 분석하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-490">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="39af2-491">예를 들어 다음 코드에서는 AJAX를 통해 추가된 양식에 대해 클라이언트 쪽 유효성 검사를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-491">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="39af2-492">`$.validator.unobtrusive.parse()` 메서드는 하나의 인수에 대해 jQuery 선택기를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-492">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="39af2-493">이 메서드는 jQuery 비간섭 유효성 검사에서 해당 선택기 내에 있는 형식의 `data-` 특성을 구문 분석하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-493">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="39af2-494">그러면 이러한 특성의 값이 jQuery 유효성 검사 플러그 인에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-494">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="39af2-495">동적 컨트롤에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="39af2-495">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="39af2-496">`$.validator.unobtrusive.parse()` 메서드는 `<input>` 및 `<select/>`와 같은 동적으로 생성된 개별 컨트롤이 아닌 전체 양식에 대해 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-496">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="39af2-497">양식을 다시 구문 분석하려면 다음 예제와 같이 이전에 양식을 구문 분석했을 때 추가된 유효성 검사 데이터를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-497">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="39af2-498">사용자 지정 클라이언트 쪽 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="39af2-498">Custom client-side validation</span></span>

<span data-ttu-id="39af2-499">사용자 지정 클라이언트 쪽 유효성 검사는 사용자 지정 jQuery 유효성 검사 어댑터에서 작동하는 `data-` HTML 특성을 생성하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-499">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="39af2-500">다음 샘플 어댑터 코드는 이 문서에서 이전에 소개한 `ClassicMovie` 및 `ClassicMovie2` 특성용으로 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-500">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="39af2-501">어댑터 작성 방법에 대한 자세한 내용은 [jQuery 유효성 검사 설명서](https://jqueryvalidation.org/documentation/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="39af2-501">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="39af2-502">지정된 필드에 대한 어댑터 사용은 `data-` 특성에 의해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-502">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="39af2-503">필드를 유효성 검사가 적용되는 것으로 표시합니다(`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="39af2-503">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="39af2-504">유효성 검사 규칙 이름 및 오류 메시지 텍스트(예: `data-val-rulename="Error message."`)를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-504">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="39af2-505">유효섬 검사기에 필요한 추가 매개 변수(예: `data-val-rulename-parm1="value"`)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-505">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="39af2-506">다음 에제에서는 샘플 앱의 `ClassicMovie` 특성에 대한 `data-` 특성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-506">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="39af2-507">앞에서 설명했듯이 [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 [HTML 도우미](xref:mvc/views/overview)는 유효성 검사 특성의 정보를 사용하여 `data-` 특성을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-507">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="39af2-508">사용자 지정 `data-` HTML 특성을 생성하는 코드를 작성하는 두 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-508">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="39af2-509">`AttributeAdapterBase<TAttribute>`에서 파생하는 클래스 및 `IValidationAttributeAdapterProvider`를 구현하는 클래스를 만들고 사용자의 특성 및 해당 어댑터를 DI에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-509">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="39af2-510">이 방법은 서버 관련 및 클라이언트 관련 유효성 검사 코드가 별도의 클래스에 있는 [단일 책임 보안 주체](https://wikipedia.org/wiki/Single_responsibility_principle)를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-510">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="39af2-511">또한 이 어댑터는 DI에 등록되었으므로 필요한 경우 DI의 다른 서비스를 사용할 수 있다는 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-511">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="39af2-512">사용자의 `ValidationAttribute` 클래스에서 `IClientModelValidator`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-512">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="39af2-513">이 방법은 특성이 서버 쪽 유효성 검사를 수행하지 않고 DI의 서비스가 필요하지 않은 경우 적절할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-513">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="39af2-514">클라이언트 쪽 유효성 검사에 대한 특성 어댑터</span><span class="sxs-lookup"><span data-stu-id="39af2-514">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="39af2-515">이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovie` 특성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-515">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="39af2-516">이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-516">To add client validation by using this method:</span></span>

1. <span data-ttu-id="39af2-517">사용자 지정 유효성 검사 특성에 대한 특성 어댑터 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-517">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="39af2-518">[AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)에서 클래스를 파생합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-518">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="39af2-519">이 예제와 같이 렌더링된 출력에 `data-` 특성을 추가하는 `AddValidation` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-519">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="39af2-520"><xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>를 구현하는 어댑터 공급자 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-520">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="39af2-521">`GetAttributeAdapter` 메서드에서 다음 예제와 같이 사용자 지정 특성을 어댑터의 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-521">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="39af2-522">DI용 어댑터 공급자를 `Startup.ConfigureServices`에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-522">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="39af2-523">클라이언트 쪽 유효성 검사를 위한 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="39af2-523">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="39af2-524">이 HTML `data-` 특성 렌더링 방법은 샘플 앱의 `ClassicMovie2` 특성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-524">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="39af2-525">이 방법을 사용하여 클라이언트 유효성 검사를 추가하려면:</span><span class="sxs-lookup"><span data-stu-id="39af2-525">To add client validation by using this method:</span></span>

* <span data-ttu-id="39af2-526">사용자 지정 유효성 검사 특성에서 `IClientModelValidator` 인터페이스를 구현하고 `AddValidation` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-526">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="39af2-527">`AddValidation` 메서드에서 다음 예제와 같이 유효성 검사를 위한 `data-` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-527">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="39af2-528">클라이언트 쪽 유효성 검사를 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="39af2-528">Disable client-side validation</span></span>

<span data-ttu-id="39af2-529">다음 코드는 MVC 보기에 클라이언트 유효성 검사를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-529">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="39af2-530">또한 Razor Pages에서:</span><span class="sxs-lookup"><span data-stu-id="39af2-530">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="39af2-531">클라이언트 유효성 검사를 사용하지 않도록 설정하는 또 다른 방법은 사용자의 *.cshtml* 파일에서 `_ValidationScriptsPartial` 참조를 주석으로 처리하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="39af2-531">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39af2-532">추가 자료</span><span class="sxs-lookup"><span data-stu-id="39af2-532">Additional resources</span></span>

* [<span data-ttu-id="39af2-533">System.ComponentModel.DataAnnotations 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="39af2-533">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="39af2-534">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="39af2-534">Model Binding</span></span>](model-binding.md)

::: moniker-end
