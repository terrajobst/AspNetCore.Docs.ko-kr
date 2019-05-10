---
title: ASP.NET Core MVC 앱에 검색 추가
author: rick-anderson
description: 기본 ASP.NET Core MVC 앱에 검색을 추가하는 방법을 보여줍니다.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: ca3b0baeddd31e10243689091d435767079bb979
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65450851"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="49e76-103">ASP.NET Core MVC 앱에 검색 추가</span><span class="sxs-lookup"><span data-stu-id="49e76-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="49e76-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="49e76-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="49e76-105">이 섹션에서는 *장르* 또는 *이름*별로 영화를 검색할 수 있는 `Index` 작업 메서드에 검색 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="49e76-106">`Index` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-106">Update the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="49e76-107">`Index` 작업 메서드의 첫 번째 줄은 영화를 선택하는 [LINQ](/dotnet/standard/using-linq) 쿼리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="49e76-108">쿼리는 이 시점에서*만* 정의되며 데이터베이스에 대해 실행되지 **않았습니다**.</span><span class="sxs-lookup"><span data-stu-id="49e76-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="49e76-109">`searchString` 매개 변수에 문자열이 포함되는 경우 영화 쿼리는 검색 문자열의 값에 대해 필터링하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="49e76-110">위의 `s => s.Title.Contains()` 코드는 [람다 식](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)입니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="49e76-111">람다 식은 메서드 기반 [LINQ](/dotnet/standard/using-linq) 쿼리에서 [Where](/dotnet/api/system.linq.enumerable.where) 메서드 또는 `Contains`(위의 코드에서 사용됨)와 같은 표준 쿼리 연산자 메서드의 인수로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="49e76-112">LINQ 쿼리는 정의될 때 또는 메서드(예: `Where`, `Contains` 또는 `OrderBy`)를 호출하여 수정될 때 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="49e76-113">대신 쿼리 실행이 지연됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="49e76-114">즉, 실제로 실현된 값이 반복되거나 `ToListAsync` 메서드가 호출될 때까지 식의 계산이 지연되는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="49e76-115">지연된 쿼리 실행에 대한 자세한 내용은 [쿼리 실행](/dotnet/framework/data/adonet/ef/language-reference/query-execution)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="49e76-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="49e76-116">참고: [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 메서드는 위에 표시된 C# 코드에서가 아닌 데이터베이스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="49e76-117">쿼리에 대한 대/소문자 구분은 데이터베이스 및 데이터 정렬에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="49e76-118">SQL Server에서 [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)는 대/소문자를 구분하는 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)로 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="49e76-119">SQLite에서 기본 데이터 정렬과 함께 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="49e76-120">`/Movies/Index`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="49e76-121">쿼리 문자열(예: `?searchString=Ghost`)을 URL에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="49e76-122">필터링된 영화가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-122">The filtered movies are displayed.</span></span>

![인덱스 보기](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="49e76-124">`id`라는 매개 변수를 포함하도록 `Index` 메서드의 서명을 변경하는 경우 `id` 매개 변수는 *Startup.cs*에서 설정된 기본 경로의 선택적 `{id}` 자리 표시자와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="49e76-125">매개 변수를 `id`로, `searchString`의 모든 항목을 `id`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="49e76-126">위의 `Index` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="49e76-127">`id` 매개 변수를 포함한 업데이트된 `Index` 메서드:</span><span class="sxs-lookup"><span data-stu-id="49e76-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="49e76-128">이제 쿼리 문자열 값이 아닌 경로 데이터(URL 세그먼트)로 검색 제목을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![URL에 추가된 단어 ghost와 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화 목록이 있는 인덱스 보기](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="49e76-130">그러나 사용자가 영화를 검색하려고 할 때마다 URL을 수정하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="49e76-131">따라서 이제 영화를 필터링하는 데 도움이 되는 UI 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="49e76-132">`Index` 메서드의 서명을 변경하여 경로 바인딩 `ID` 매개 변수 전달 방법을 테스트하는 경우 `searchString`이라는 매개 변수를 사용하도록 다시 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="49e76-133">*Views/Movies/Index.cshtml* 파일을 열고 아래에서 강조 표시된 `<form>` 표시를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="49e76-134">HTML `<form>` 태그는 [양식 태그 도우미](xref:mvc/views/working-with-forms)를 사용하므로 양식을 제출하는 경우 필터 문자열은 영화 컨트롤러의 `Index` 작업에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="49e76-135">변경 내용을 저장하고 필터를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-135">Save your changes and then test the filter.</span></span>

![제목 필터 텍스트 상자에 단어 ghost를 입력하여 인덱스 보기](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="49e76-137">예상할 수 있는 대로 `Index` 메서드의 `[HttpPost]` 오버로드가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="49e76-138">메서드가 앱의 상태를 변경하지 않고 데이터를 필터링하기 때문에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="49e76-139">다음 `[HttpPost] Index` 메서드를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="49e76-140">`notUsed` 매개 변수는 `Index` 메서드의 오버로드를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="49e76-141">자습서의 뒷부분에서 이에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="49e76-142">이 메서드를 추가하는 경우 작업 호출자는 `[HttpPost] Index` 메서드와 일치하고 `[HttpPost] Index` 메서드는 아래 이미지에 나와 있는 대로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![HttpPost 인덱스에서의 애플리케이션 응답을 포함한 브라우저 창: ghost에 대한 필터](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="49e76-144">그러나 이 `[HttpPost]` 버전의 `Index` 메서드를 추가하는 경우에도 이를 모두 구현하는 방법은 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="49e76-145">특정 검색을 책갈피로 설정하거나 동일하게 필터링된 영화 목록을 보기 위해 클릭할 수 있는 링크를 친구에게 보내려고 한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="49e76-146">HTTP POST 요청의 URL은 GET 요청의 URL(localhost:xxxxx/Movies/Index)과 동일합니다. URL에는 검색 정보가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="49e76-147">검색 문자열 정보는 [양식 필드 값](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)으로 서버에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="49e76-148">브라우저 개발자 도구 또는 뛰어난 [Fiddler 도구](http://www.telerik.com/fiddler)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](http://www.telerik.com/fiddler).</span></span> <span data-ttu-id="49e76-149">아래 이미지에서는 Chrome 브라우저 개발자 도구를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-149">The image below shows the Chrome browser Developer tools:</span></span>

![Microsoft Edge에 있는 개발자 도구의 네트워크 탭은 ghost의 searchString 값을 가진 요청 본문을 보여줍니다.](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="49e76-151">요청 본문에서 검색 매개 변수 및 [XSRF](xref:security/anti-request-forgery) 토큰을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="49e76-152">이전 자습서에서 설명했듯이 [양식 태그 도우미](xref:mvc/views/working-with-forms)는 [XSRF](xref:security/anti-request-forgery) 위조 방지 토큰을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="49e76-153">컨트롤러 메서드에서 토큰 유효성을 검사할 필요가 없도록 데이터를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="49e76-154">검색 매개 변수가 URL이 아닌 요청 본문에 있기 때문에 책갈피에 해당 검색 정보를 캡처하거나 다른 사용자와 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="49e76-155">요청을 `HTTP GET`로 지정하여 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-155">Fix this by specifying the request should be `HTTP GET`:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="49e76-156">이제 검색을 제출할 때 URL에는 검색 쿼리 문자열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="49e76-157">`HttpPost Index` 메서드가 있는 경우에도 검색은 `HttpGet Index` 작업 메서드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![URL에서 searchString=ghost 및 Ghostbusters 및 Ghostbusters 2라는 두 개의 반환된 영화를 표시하는 브라우저 창은 단어 ghost를 포함합니다.](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="49e76-159">다음 표시는 `form` 태그에 대한 변경 내용을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-159">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="add-search-by-genre"></a><span data-ttu-id="49e76-160">장르별 검색 추가</span><span class="sxs-lookup"><span data-stu-id="49e76-160">Add Search by genre</span></span>

<span data-ttu-id="49e76-161">다음 `MovieGenreViewModel` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="49e76-162">영화 장르 보기 모델은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="49e76-163">영화 목록</span><span class="sxs-lookup"><span data-stu-id="49e76-163">A list of movies.</span></span>
* <span data-ttu-id="49e76-164">장르 목록을 포함한 `SelectList`</span><span class="sxs-lookup"><span data-stu-id="49e76-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="49e76-165">이를 통해 사용자는 목록에서 장르를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="49e76-166">선택한 장르가 포함된 `MovieGenre`</span><span class="sxs-lookup"><span data-stu-id="49e76-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="49e76-167">사용자가 검색 텍스트 상자에 입력한 텍스트가 포함된 `SearchString`.</span><span class="sxs-lookup"><span data-stu-id="49e76-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="49e76-168">`MoviesController.cs`에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="49e76-169">다음 코드는 데이터베이스에서 모든 장르를 검색하는 `LINQ` 쿼리입니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="49e76-170">특정 장르를 프로젝트하여 장르의 `SelectList`를 생성합니다(선택 목록에 중복 장르가 없도록 함).</span><span class="sxs-lookup"><span data-stu-id="49e76-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="49e76-171">사용자가 항목을 검색하면 검색 상자에 검색 값이 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="49e76-172">인덱스 보기에 장르별 검색 추가</span><span class="sxs-lookup"><span data-stu-id="49e76-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="49e76-173">다음과 같이 `Index.cshtml`을 업그레이드합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-173">Update `Index.cshtml` as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="49e76-174">다음 HTML 도우미에서 사용되는 람다 식을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="49e76-175">이전 코드에서 `DisplayNameFor` HTML 도우미는 람다 식에서 참조되는 `Title` 속성을 검사하여 표시 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="49e76-176">람다 식을 평가하지 않고 검사하기 때문에 `model`, `model.Movies` 또는 `model.Movies[0]`가 `null`이거나 비어 있을 경우 액세스 위반을 수신하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="49e76-177">람다 식이 계산될 경우(예: `@Html.DisplayFor(modelItem => item.Title)`) 모델의 속성 값이 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="49e76-178">장르별, 동영상 제목별 및 둘 다로 검색하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="49e76-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2의 결과를 보여주는 브라우저 창](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="49e76-180">[이전](controller-methods-views.md)
> [다음](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="49e76-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
