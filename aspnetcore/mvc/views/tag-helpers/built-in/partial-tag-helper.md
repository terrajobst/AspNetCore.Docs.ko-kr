---
title: ASP.NET Core의 부분 태그 도우미
author: scottaddie
description: ASP.NET Core 부분 태그 도우미와 부분 보기를 렌더링할 때 각 특성이 수행하는 역할을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 116fce7af5dc138fbbb0351a4f38f59e88c8f338
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468684"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="84d65-103">ASP.NET Core의 부분 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="84d65-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="84d65-104">작성자: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="84d65-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="84d65-105">태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84d65-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="84d65-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84d65-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="84d65-107">개요</span><span class="sxs-lookup"><span data-stu-id="84d65-107">Overview</span></span>

<span data-ttu-id="84d65-108">부분 태그 도우미는 Razor 페이지 및 MVC 앱에서 [부분 보기](xref:mvc/views/partial)를 렌더링하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="84d65-109">고려 사항:</span><span class="sxs-lookup"><span data-stu-id="84d65-109">Consider that it:</span></span>

* <span data-ttu-id="84d65-110">ASP.NET Core 2.1 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="84d65-111">[HTML 도우미 구문](xref:mvc/views/partial#reference-a-partial-view) 대신 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="84d65-112">부분 보기를 비동기적으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="84d65-113">부분 보기 렌더링을 위한 HTML 도우미 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-113">The HTML Helper options for rendering a partial view include:</span></span>

* [<span data-ttu-id="84d65-114">@await Html.PartialAsync</span><span class="sxs-lookup"><span data-stu-id="84d65-114">@await Html.PartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [<span data-ttu-id="84d65-115">@await Html.RenderPartialAsync</span><span class="sxs-lookup"><span data-stu-id="84d65-115">@await Html.RenderPartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [@Html.Partial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [@Html.RenderPartial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="84d65-116">*제품* 모델은 이 문서 전반의 샘플에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-116">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="84d65-117">부분 태그 도우미의 특성 목록은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-117">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="84d65-118">name</span><span class="sxs-lookup"><span data-stu-id="84d65-118">name</span></span>

<span data-ttu-id="84d65-119">`name` 특성은 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-119">The `name` attribute is required.</span></span> <span data-ttu-id="84d65-120">이 특성은 렌더링할 부분 뷰의 이름 또는 경로를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-120">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="84d65-121">부분 뷰의 이름을 지정할 경우 [뷰 탐색](xref:mvc/views/overview#view-discovery) 과정이 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-121">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="84d65-122">명시적인 경로가 지정될 경우에는 이 과정이 생략됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-122">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="84d65-123">허용되는 모든 `name` 값은 [부분 보기 검색](xref:mvc/views/partial#partial-view-discovery)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84d65-123">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="84d65-124">다음 태그는 명시적인 경로를 사용해서 *Shared* 폴더의 *_ProductPartial.cshtml*을 로드하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-124">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="84d65-125">[for](#for) 특성을 사용하면 바인딩을 위한 모델이 부분 뷰에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-125">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="84d65-126">for</span><span class="sxs-lookup"><span data-stu-id="84d65-126">for</span></span>

<span data-ttu-id="84d65-127">`for` 특성은 현재 모델과 비교 평가할 [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression)을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-127">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="84d65-128">`ModelExpression`은 `@Model.` 구문을 추론합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-128">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="84d65-129">예를 들어 `for="@Model.Product"` 대신 `for="Product"`를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-129">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="84d65-130">이 기본 추론 동작은 인라인 식을 정의하는 `@` 기호를 사용하여 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-130">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="84d65-131">다음 태그는 *_ProductPartial.cshtml*을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-131">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="84d65-132">부분 뷰는 연결된 페이지 모델의 `Product` 속성과 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-132">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="84d65-133">model</span><span class="sxs-lookup"><span data-stu-id="84d65-133">model</span></span>

<span data-ttu-id="84d65-134">`model` 특성은 부분 뷰에 전달할 모델의 인스턴스를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-134">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="84d65-135">`model` 특성은 [for](#for) 특성과 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-135">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="84d65-136">다음 표시에서는 새 `Product` 개체가 인스턴스화되고 바인딩을 위해 `model` 특성에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-136">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="84d65-137">view-data</span><span class="sxs-lookup"><span data-stu-id="84d65-137">view-data</span></span>

<span data-ttu-id="84d65-138">`view-data` 특성은 부분 보기에 전달할 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-138">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="84d65-139">다음 표시를 사용하면 부분 보기에서 전체 ViewData 컬렉션에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-139">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="84d65-140">위의 코드에서 `IsNumberReadOnly` 키 값은 `true`로 설정되고 ViewData 컬렉션에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-140">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="84d65-141">따라서 다음 부분 뷰 내에서 `ViewData["IsNumberReadOnly"]`에 접근할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-141">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="84d65-142">이 예제에서 `ViewData["IsNumberReadOnly"]` 값은 *Number* 필드가 읽기 전용으로 표시될지 여부를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-142">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="84d65-143">HTML 도우미에서 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="84d65-143">Migrate from an HTML Helper</span></span>

<span data-ttu-id="84d65-144">다음 비동기 HTML 도우미 예제를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="84d65-144">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="84d65-145">제품 컬렉션이 반복되어 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-145">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="84d65-146">`PartialAsync` 메서드의 첫 번째 매개 변수에 따라, *_ProductPartial.cshtml* 부분 보기를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-146">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="84d65-147">`Product` 모델의 인스턴스가 바인딩을 위한 모델이 부분 보기에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-147">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="84d65-148">다음 부분 태그 도우미는 `PartialAsync` HTML 도우미와 동일한 비동기 렌더링 동작을 합니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-148">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="84d65-149">부분 보기에 바인딩하기 위해 `Product` 모델 인스턴스가 `model` 특성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="84d65-149">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="84d65-150">추가 자료</span><span class="sxs-lookup"><span data-stu-id="84d65-150">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
