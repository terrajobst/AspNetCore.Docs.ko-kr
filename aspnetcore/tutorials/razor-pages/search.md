---
title: ASP.NET Core Razor 페이지에 검색 추가
author: rick-anderson
description: ASP.NET Core Razor 페이지에 검색을 추가하는 방법을 보여 줍니다.
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8228207b0f37a6923b29891ac3115dd0be115501
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78651045"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="70d6c-103">ASP.NET Core Razor 페이지에 검색 추가</span><span class="sxs-lookup"><span data-stu-id="70d6c-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="70d6c-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="70d6c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="70d6c-105">다음 섹션에서는 *장르* 또는 *이름*으로 영화 검색이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="70d6c-106">*Pages/Movies/Index.cshtml.cs*에 강조 표시된 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="70d6c-107">`SearchString`: 사용자가 검색 텍스트 상자에 입력하는 텍스트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="70d6c-108">`SearchString`에는 [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="70d6c-109">`[BindProperty]`는 양식 값 및 쿼리 문자열을 속성과 동일한 이름으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="70d6c-110">`(SupportsGet = true)`는 GET 요청을 바인딩하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="70d6c-111">`Genres`: 장르 목록을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="70d6c-112">`Genres`를 통해 사용자는 목록에서 장르를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="70d6c-113">`SelectList`는 `using Microsoft.AspNetCore.Mvc.Rendering;`이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="70d6c-114">`MovieGenre`: 사용자가 선택하는 특정 장르를 포함합니다(예: "서부 영화").</span><span class="sxs-lookup"><span data-stu-id="70d6c-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="70d6c-115">`Genres` 및 `MovieGenre`는 이 자습서의 뒷부분에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="70d6c-116">인덱스 페이지의 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="70d6c-117">`OnGetAsync` 메서드의 첫 번째 줄은 동영상을 선택하는 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="70d6c-118">쿼리는 이 시점에서*만* 정의되며 데이터베이스에 대해 실행되지 **않았습니다**.</span><span class="sxs-lookup"><span data-stu-id="70d6c-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="70d6c-119">`SearchString` 속성이 Null 또는 비어 있는 경우 영화 쿼리는 검색 문자열에 대해 필터링하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="70d6c-120">`s => s.Title.Contains()` 코드는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)입니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="70d6c-121">람다 식은 메서드 기반 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리에서 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="70d6c-122">LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="70d6c-123">대신 쿼리 실행이 지연됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="70d6c-124">즉, 실현된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연되는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="70d6c-125">자세한 내용은 [쿼리 실행](/dotnet/framework/data/adonet/ef/language-reference/query-execution)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="70d6c-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="70d6c-126">[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 메서드는 C# 코드에서가 아닌 데이터베이스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="70d6c-127">쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="70d6c-128">SQL Server에서 `Contains`는 대/소문자를 구분하는 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)로 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="70d6c-129">SQLite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="70d6c-130">Movies 페이지로 이동하고 `?searchString=Ghost`와 같은 쿼리 문자열을 URL에 추가합니다(예: `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="70d6c-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="70d6c-131">필터링된 영화가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-131">The filtered movies are displayed.</span></span>

![Index 보기](search/_static/ghost.png)

<span data-ttu-id="70d6c-133">다음 경로 템플릿이 인덱스 페이지에 추가되는 경우 검색 문자열이 URL 세그먼트로 전달될 수 있습니다(예: `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="70d6c-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="70d6c-134">이전 경로 제약 조건을 통해 쿼리 문자열 값 대신 경로 데이터(URL 세그먼트)로 제목을 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="70d6c-135">`?`에서 `"{searchString?}"`는 선택적 경로 매개 변수임을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![URL에 ghost라는 단어가 추가되고 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화 목록이 있는 Index 보기](search/_static/g2.png)

<span data-ttu-id="70d6c-137">ASP.NET Core 런타임은 [모델 바인딩](xref:mvc/models/model-binding)을 사용하여 쿼리 문자열(`SearchString`) 또는 경로 데이터(`?searchString=Ghost`)에서 `https://localhost:5001/Movies/Ghost` 속성의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="70d6c-138">모델 바인딩은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="70d6c-139">그러나 사용자가 동영상 검색을 위해 URL을 수정하는 것을 기대할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="70d6c-140">이 단계에서 동영상을 필터링하도록 UI가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="70d6c-141">경로 제약 조건 `"{searchString?}"`을 추가한 경우 이를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="70d6c-142">*Pages/Movies/Index.cshtml* 파일을 열고 다음 코드에서 강조 표시된 `<form>` 표시를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="70d6c-143">HTML `<form>` 태그는 다음과 같은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="70d6c-144">[형식 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="70d6c-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="70d6c-145">양식이 제출되면 쿼리 문자열을 통해 필터 문자열이 *Pages/Movies/Index* 페이지로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="70d6c-146">입력 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="70d6c-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="70d6c-147">변경 내용을 저장하고 필터를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-147">Save the changes and test the filter.</span></span>

![Title 필터 텍스트 상자에 ghost라는 단어를 입력한 Index 보기](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="70d6c-149">장르별 검색</span><span class="sxs-lookup"><span data-stu-id="70d6c-149">Search by genre</span></span>

<span data-ttu-id="70d6c-150">`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="70d6c-151">다음 코드는 데이터베이스에서 모든 장르를 검색하는 LINQ 쿼리입니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="70d6c-152">장르의 `SelectList`는 고유 장르를 프로젝션함으로써 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="70d6c-153">Razor 페이지에 장르별 검색 추가</span><span class="sxs-lookup"><span data-stu-id="70d6c-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="70d6c-154">다음과 같이 *Index.cshtml*을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="70d6c-155">장르별, 동영상 제목별 및 둘 다로 검색하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70d6c-156">추가 자료</span><span class="sxs-lookup"><span data-stu-id="70d6c-156">Additional resources</span></span>

* [<span data-ttu-id="70d6c-157">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="70d6c-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="70d6c-158">[이전: 페이지 업데이트](xref:tutorials/razor-pages/da1)
> [다음: 새 필드 추가](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="70d6c-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="70d6c-159">다음 섹션에서는 *장르* 또는 *이름*으로 영화 검색이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="70d6c-160">*Pages/Movies/Index.cshtml.cs*에 강조 표시된 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="70d6c-161">`SearchString`: 사용자가 검색 텍스트 상자에 입력하는 텍스트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="70d6c-162">`SearchString`에는 [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="70d6c-163">`[BindProperty]`는 양식 값 및 쿼리 문자열을 속성과 동일한 이름으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="70d6c-164">`(SupportsGet = true)`는 GET 요청을 바인딩하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="70d6c-165">`Genres`: 장르 목록을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="70d6c-166">`Genres`를 통해 사용자는 목록에서 장르를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="70d6c-167">`SelectList`는 `using Microsoft.AspNetCore.Mvc.Rendering;`이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="70d6c-168">`MovieGenre`: 사용자가 선택하는 특정 장르를 포함합니다(예: "서부 영화").</span><span class="sxs-lookup"><span data-stu-id="70d6c-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="70d6c-169">`Genres` 및 `MovieGenre`는 이 자습서의 뒷부분에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="70d6c-170">인덱스 페이지의 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="70d6c-171">`OnGetAsync` 메서드의 첫 번째 줄은 동영상을 선택하는 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="70d6c-172">쿼리는 이 시점에서*만* 정의되며 데이터베이스에 대해 실행되지 **않았습니다**.</span><span class="sxs-lookup"><span data-stu-id="70d6c-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="70d6c-173">`SearchString` 속성이 Null 또는 비어 있는 경우 영화 쿼리는 검색 문자열에 대해 필터링하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="70d6c-174">`s => s.Title.Contains()` 코드는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)입니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="70d6c-175">람다 식은 메서드 기반 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 쿼리에서 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="70d6c-176">LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="70d6c-177">대신 쿼리 실행이 지연됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="70d6c-178">즉, 실현된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연되는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="70d6c-179">자세한 내용은 [쿼리 실행](/dotnet/framework/data/adonet/ef/language-reference/query-execution)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="70d6c-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="70d6c-180">**참고:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 메서드는 C# 코드에서가 아닌 데이터베이스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="70d6c-181">쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="70d6c-182">SQL Server에서 `Contains`는 대/소문자를 구분하는 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)로 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="70d6c-183">SQLite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="70d6c-184">Movies 페이지로 이동하고 `?searchString=Ghost`와 같은 쿼리 문자열을 URL에 추가합니다(예: `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="70d6c-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="70d6c-185">필터링된 영화가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-185">The filtered movies are displayed.</span></span>

![Index 보기](search/_static/ghost.png)

<span data-ttu-id="70d6c-187">다음 경로 템플릿이 인덱스 페이지에 추가되는 경우 검색 문자열이 URL 세그먼트로 전달될 수 있습니다(예: `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="70d6c-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="70d6c-188">이전 경로 제약 조건을 통해 쿼리 문자열 값 대신 경로 데이터(URL 세그먼트)로 제목을 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="70d6c-189">`?`에서 `"{searchString?}"`는 선택적 경로 매개 변수임을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![URL에 ghost라는 단어가 추가되고 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화 목록이 있는 Index 보기](search/_static/g2.png)

<span data-ttu-id="70d6c-191">ASP.NET Core 런타임은 [모델 바인딩](xref:mvc/models/model-binding)을 사용하여 쿼리 문자열(`SearchString`) 또는 경로 데이터(`?searchString=Ghost`)에서 `https://localhost:5001/Movies/Ghost` 속성의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="70d6c-192">모델 바인딩은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="70d6c-193">그러나 사용자가 동영상 검색을 위해 URL을 수정하는 것을 기대할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="70d6c-194">이 단계에서 동영상을 필터링하도록 UI가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="70d6c-195">경로 제약 조건 `"{searchString?}"`을 추가한 경우 이를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="70d6c-196">*Pages/Movies/Index.cshtml* 파일을 열고 다음 코드에서 강조 표시된 `<form>` 표시를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="70d6c-197">HTML `<form>` 태그는 다음과 같은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="70d6c-198">[형식 태그 도우미](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="70d6c-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="70d6c-199">양식이 제출되면 쿼리 문자열을 통해 필터 문자열이 *Pages/Movies/Index* 페이지로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="70d6c-200">입력 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="70d6c-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="70d6c-201">변경 내용을 저장하고 필터를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-201">Save the changes and test the filter.</span></span>

![Title 필터 텍스트 상자에 ghost라는 단어를 입력한 Index 보기](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="70d6c-203">장르별 검색</span><span class="sxs-lookup"><span data-stu-id="70d6c-203">Search by genre</span></span>

<span data-ttu-id="70d6c-204">`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="70d6c-205">다음 코드는 데이터베이스에서 모든 장르를 검색하는 LINQ 쿼리입니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="70d6c-206">장르의 `SelectList`는 고유 장르를 프로젝션함으로써 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="70d6c-207">Razor 페이지에 장르별 검색 추가</span><span class="sxs-lookup"><span data-stu-id="70d6c-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="70d6c-208">다음과 같이 *Index.cshtml*을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="70d6c-209">장르별, 동영상 제목별 및 둘 다로 검색하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="70d6c-210">위의 코드는 [Select 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper) 및 Option 태그 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="70d6c-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70d6c-211">추가 자료</span><span class="sxs-lookup"><span data-stu-id="70d6c-211">Additional resources</span></span>

* [<span data-ttu-id="70d6c-212">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="70d6c-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="70d6c-213">[이전: 페이지 업데이트](xref:tutorials/razor-pages/da1)
> [다음: 새 필드 추가](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="70d6c-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
