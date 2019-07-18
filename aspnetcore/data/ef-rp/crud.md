---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - CRUD - 2/8
author: rick-anderson
description: EF Core를 사용한 만들기, 읽기, 업데이트, 삭제 방법을 보여 줍니다.
ms.author: riande
ms.date: 06/30/2017
uid: data/ef-rp/crud
ms.openlocfilehash: 2e2aaa3c84759bde39ec3f46ff5ba8699f6c219b
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223837"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a><span data-ttu-id="c0ab4-103">ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - CRUD - 2/8</span><span class="sxs-lookup"><span data-stu-id="c0ab4-103">Razor Pages with EF Core in ASP.NET Core - CRUD - 2 of 8</span></span>

<span data-ttu-id="c0ab4-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0ab4-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="c0ab4-105">이 자습서에서는 스캐폴드된 CRUD(만들기, 읽기, 업데이트, 삭제) 코드를 검토 및 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="c0ab4-106">복잡성을 최소화하고 이러한 자습서의 초점을 EF Core로 유지하기 위해 페이지 모델에 EF Core 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-106">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="c0ab4-107">일부 개발자는 UI(Razor 페이지) 및 데이터 액세스 계층 간에 추상화 계층을 생성하도록 서비스 계층 또는 리포지토리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-107">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="c0ab4-108">이 자습서에서는 *Student* 폴더에서 Razor Pages 만들기, 편집, 삭제 및 세부 정보가 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-108">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="c0ab4-109">스캐폴드된 코드는 만들기, 편집 및 삭제 페이지에 대해 다음과 같은 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-109">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="c0ab4-110">HTTP GET 메서드 `OnGetAsync`로 요청된 데이터를 가져오고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-110">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="c0ab4-111">HTTP POST 메서드 `OnPostAsync`로 데이터의 변경 내용을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-111">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="c0ab4-112">인덱스 및 세부 정보 페이지는 HTTP GET 메서드 `OnGetAsync`로 요청된 데이터를 가져오고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-112">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="c0ab4-113">SingleOrDefaultAsync 및 FirstOrDefaultAsync</span><span class="sxs-lookup"><span data-stu-id="c0ab4-113">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="c0ab4-114">생성된 코드는 [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)보다 일반적으로 선호되는 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-114">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="c0ab4-115">하나의 엔터티를 페치할 때 `SingleOrDefaultAsync`보다 `FirstOrDefaultAsync`가 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-115">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="c0ab4-116">쿼리에서 반환된 엔터티가 두 개 이상이 아닌지 코드에서 확인해야 하는 경우 제외.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-116">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="c0ab4-117">`SingleOrDefaultAsync`는 더 많은 데이터를 페치하고 불필요한 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-117">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="c0ab4-118">`SingleOrDefaultAsync`는 필터 파트에 맞는 엔터티가 둘 이상인 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-118">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="c0ab4-119">`FirstOrDefaultAsync`는 필터 파트에 맞는 엔터티가 둘 이상인 경우 예외를 throw하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-119">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="c0ab4-120">FindAsync</span><span class="sxs-lookup"><span data-stu-id="c0ab4-120">FindAsync</span></span>

<span data-ttu-id="c0ab4-121">스캐폴드된 코드의 대부분에서 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___)를 `FirstOrDefaultAsync` 대신 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-121">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="c0ab4-122">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="c0ab4-122">`FindAsync`:</span></span>

* <span data-ttu-id="c0ab4-123">PK(기본 키)가 있는 엔터티를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-123">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="c0ab4-124">PK가 있는 엔터티를 컨텍스트에서 추적하는 경우 요청 없이 DB에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-124">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="c0ab4-125">단순하고 간결합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-125">Is simple and concise.</span></span>
* <span data-ttu-id="c0ab4-126">단일 엔터티를 조회하도록 최적화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-126">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="c0ab4-127">일부 상황에서는 성능 이점을 가질 수 있지만 일반적인 웹앱에서는 거의 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-127">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="c0ab4-128">[SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 대신 [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)를 암시적으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-128">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="c0ab4-129">하지만 다른 엔터티를 `Include`하려는 경우 `FindAsync`는 더 이상 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-129">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="c0ab4-130">즉, `FindAsync`를 취소하고 앱 진행에 따라 쿼리로 이동해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-130">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="c0ab4-131">세부 정보 사용자 지정 페이지</span><span class="sxs-lookup"><span data-stu-id="c0ab4-131">Customize the Details page</span></span>

<span data-ttu-id="c0ab4-132">`Pages/Students` 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-132">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="c0ab4-133">**편집**, **세부 정보** 및 **삭제** 링크는 *Pages/Students/Index.cshtml* 파일에서 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-133">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="c0ab4-134">앱을 실행하고 **세부 정보** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-134">Run the app and select a **Details** link.</span></span> <span data-ttu-id="c0ab4-135">URL은 `http://localhost:5000/Students/Details?id=2` 양식입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-135">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="c0ab4-136">학생 ID는 쿼리 문자열(`?id=2`)을 사용하여 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-136">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="c0ab4-137">`"{id:int}"` 경로 템플릿을 사용하도록 편집, 세부 정보 및 삭제 Razor 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-137">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="c0ab4-138">이러한 각 페이지에 대한 page 지시문을 `@page`에서 `@page "{id:int}"`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-138">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="c0ab4-139">정수 경로 값을 포함하지 **않는** “{id:int}” 경로 템플릿이 있는 페이지에 대한 요청은 HTTP 404(찾을 수 없음) 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-139">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="c0ab4-140">예를 들어 `http://localhost:5000/Students/Details`는 404 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-140">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="c0ab4-141">ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-141">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="c0ab4-142">앱을 실행하고, 세부 정보 링크를 클릭하고, URL이 경로 데이터로 ID를 전달하는지 확인합니다(`http://localhost:5000/Students/Details/2`).</span><span class="sxs-lookup"><span data-stu-id="c0ab4-142">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="c0ab4-143">전체적으로 `@page`를 `@page "{id:int}"`로 변경하지 마십시오. 변경하면 홈 및 만들기 페이지에 대한 링크가 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-143">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="c0ab4-144">관련 데이터 추가</span><span class="sxs-lookup"><span data-stu-id="c0ab4-144">Add related data</span></span>

<span data-ttu-id="c0ab4-145">학생 인덱스 페이지에 대한 스캐폴드된 코드는 `Enrollments` 속성을 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-145">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="c0ab4-146">이 섹션에서 `Enrollments` 컬렉션의 콘텐츠는 세부 정보 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-146">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="c0ab4-147">*Pages/Students/Details.cshtml.cs*의 `OnGetAsync` 메서드는 `FirstOrDefaultAsync` 메서드를 사용하여 단일 `Student` 엔터티를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-147">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="c0ab4-148">다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-148">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="c0ab4-149">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 및 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 메서드로 인해 컨텍스트가 `Student.Enrollments` 탐색 속성 및 각 등록 내에서 `Enrollment.Course` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-149">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="c0ab4-150">이러한 메서드는 읽기 관련 데이터 자습서에서 자세히 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-150">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="c0ab4-151">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 메서드는 반환된 엔터티가 현재 컨텍스트에서 업데이트되지 않는 시나리오에서 성능을 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-151">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="c0ab4-152">`AsNoTracking`은 이 자습서의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-152">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="c0ab4-153">세부 정보 페이지에 관련된 등록 표시</span><span class="sxs-lookup"><span data-stu-id="c0ab4-153">Display related enrollments on the Details page</span></span>

<span data-ttu-id="c0ab4-154">*Pages/Students/Details.cshtml*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-154">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="c0ab4-155">등록의 목록을 표시하려면 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-155">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="c0ab4-156">코드를 붙여넣은 후 코드 들여쓰기가 잘못된 경우 CTRL-K-D를 눌러 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-156">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="c0ab4-157">위의 코드는 `Enrollments` 탐색 속성의 엔터티를 통해 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-157">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="c0ab4-158">각 등록의 경우 강좌 제목과 등급을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-158">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="c0ab4-159">강좌 제목은 등록 엔터티의 `Course` 탐색 속성에 저장되어 있는 강좌 엔터티에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-159">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="c0ab4-160">앱을 실행하고, **학생** 탭을 클릭하고, 학생에 대한 **세부 정보** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-160">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="c0ab4-161">선택한 학생에 대한 강좌 및 등급의 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-161">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="c0ab4-162">만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="c0ab4-162">Update the Create page</span></span>

<span data-ttu-id="c0ab4-163">다음 코드로 *Pages/Students/Create.cshtml.cs*의 `OnPostAsync` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-163">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="c0ab4-164">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="c0ab4-164">TryUpdateModelAsync</span></span>

<span data-ttu-id="c0ab4-165">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 코드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-165">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="c0ab4-166">위의 코드에서 `TryUpdateModelAsync<Student>`는 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)의 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 속성에서 게시된 양식 값을 사용하여 `emptyStudent` 개체를 업데이트하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-166">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="c0ab4-167">`TryUpdateModelAsync`는 나열된 속성만 업데이트합니다(`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span><span class="sxs-lookup"><span data-stu-id="c0ab4-167">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="c0ab4-168">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="c0ab4-168">In the preceding sample:</span></span>

* <span data-ttu-id="c0ab4-169">두 번째 인수(`"student", // Prefix`)는 값 조회에 사용하는 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-169">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="c0ab4-170">대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-170">It's not case sensitive.</span></span>
* <span data-ttu-id="c0ab4-171">게시된 양식 값은 [모델 바인딩](xref:mvc/models/model-binding)을 사용한 `Student` 모델의 형식으로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-171">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="c0ab4-172">초과 게시</span><span class="sxs-lookup"><span data-stu-id="c0ab4-172">Overposting</span></span>

<span data-ttu-id="c0ab4-173">게시된 값으로 `TryUpdateModel`을 사용하는 것은 초과 게시가 방지되는 보안 모범 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-173">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="c0ab4-174">예를 들어, 학생 엔터티가 이 웹 페이지가 업데이트하거나 추가해서는 안 되는 `Secret` 속성을 포함한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-174">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="c0ab4-175">앱에 만들기/업데이트 Razor 페이지의 `Secret` 필드가 없는 경우에도 해커는 초과 게시를 통해 `Secret` 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-175">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="c0ab4-176">해커가 Fiddler와 같은 도구를 사용하거나 일부 JavaScript를 작성하여 `Secret` 양식 값을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-176">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="c0ab4-177">원본 코드는 학생 인스턴스를 만들 때 모델 바인더가 사용하는 필드를 제한하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-177">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="c0ab4-178">해커가 `Secret` 양식 필드에 대해 지정한 모든 값은 DB에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-178">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="c0ab4-179">다음 이미지에는 게시된 양식 값에 `Secret` 필드를 추가(값 “OverPost” 사용)하는 Fiddler 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-179">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![암호 필드를 추가 하는 Fiddler](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="c0ab4-181">값 “OverPost”가 삽입된 된 행의 `Secret` 속성에 성공적으로 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-181">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="c0ab4-182">앱 디자이너는 `Secret` 속성이 만들기 페이지를 통해 설정되는 것을 의도하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-182">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="c0ab4-183">뷰 모델</span><span class="sxs-lookup"><span data-stu-id="c0ab4-183">View model</span></span>

<span data-ttu-id="c0ab4-184">뷰 모델은 일반적으로 애플리케이션에서 사용되는 모델에 포함된 속성의 하위 집합을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-184">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="c0ab4-185">애플리케이션 모델은 흔히 도메인 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-185">The application model is often called the domain model.</span></span> <span data-ttu-id="c0ab4-186">도메인 모델은 일반적으로 DB의 해당 엔터티에 필요한 모든 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-186">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="c0ab4-187">뷰 모델은 UI 계층에 필요한 속성만 포함합니다(예: 만들기 페이지).</span><span class="sxs-lookup"><span data-stu-id="c0ab4-187">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="c0ab4-188">뷰 모델 외에도 일부 앱은 바인딩 모델 또는 입력 모델을 사용하여 Razor 페이지 페이지 모델 클래스와 브라우저 간에 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-188">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="c0ab4-189">다음 `Student` 뷰 모델을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-189">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="c0ab4-190">뷰 모델은 초과 게시를 방지하기 위한 다른 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-190">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="c0ab4-191">뷰 모델은 확인(표시)하거나 업데이트하는 속성만 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-191">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="c0ab4-192">다음 코드는 `StudentVM` 뷰 모델을 사용하여 새 학생을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-192">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="c0ab4-193">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 메서드는 다른 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 개체의 값을 읽어서 이 개체의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-193">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="c0ab4-194">`SetValues`는 속성 이름 일치를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-194">`SetValues` uses property name matching.</span></span> <span data-ttu-id="c0ab4-195">뷰 모델 형식은 모델 형식과 연결될 필요는 없으며 일치하는 속성만 있으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-195">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="c0ab4-196">`StudentVM`을 사용하려면 `Student` 대신 `StudentVM`을 사용하도록 [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml)을 업데이트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-196">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="c0ab4-197">Razor 페이지에서 `PageModel` 파생 클래스는 뷰 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-197">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="c0ab4-198">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="c0ab4-198">Update the Edit page</span></span>

<span data-ttu-id="c0ab4-199">편집 페이지를 위한 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-199">Update the page model for the Edit page.</span></span> <span data-ttu-id="c0ab4-200">주요 변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-200">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="c0ab4-201">코드 변경은 만들기 페이지와 유사합니다. 단, 다음과 같은 몇 가지 예외가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-201">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="c0ab4-202">`OnPostAsync`에는 선택적 `id` 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-202">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="c0ab4-203">현재 학생은 빈 학생을 만드는 대신 DB에서 페치됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-203">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="c0ab4-204">`FirstOrDefaultAsync`는 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)로 대체되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-204">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="c0ab4-205">`FindAsync`는 기본 키의 엔터티를 선택하는 경우 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-205">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="c0ab4-206">자세한 내용은 [FindAsync](#FindAsync)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-206">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="c0ab4-207">편집 및 만들기 페이지 테스트</span><span class="sxs-lookup"><span data-stu-id="c0ab4-207">Test the Edit and Create pages</span></span>

<span data-ttu-id="c0ab4-208">몇 가지 학생 엔터티를 만들고 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-208">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="c0ab4-209">엔터티 상태</span><span class="sxs-lookup"><span data-stu-id="c0ab4-209">Entity States</span></span>

<span data-ttu-id="c0ab4-210">DB 컨텍스트는 메모리의 엔터티가 해당하는 DB의 행과 동기화하는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-210">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="c0ab4-211">DB 컨텍스트 동기화 정보는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 수행할 작업을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-211">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="c0ab4-212">예를 들어 새 엔터티가 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 메서드에 전달된 경우 해당 엔터티의 상태가 [추가됨](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-212">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="c0ab4-213">`SaveChangesAsync`가 호출되면 DB 컨텍스트가 SQL INSERT 명령을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-213">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="c0ab4-214">엔터티는 [다음 상태](/dotnet/api/microsoft.entityframeworkcore.entitystate) 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-214">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="c0ab4-215">`Added`: 엔터티가 아직 DB에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-215">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="c0ab4-216">`SaveChanges` 메서드가 INSERT 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-216">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="c0ab4-217">`Unchanged`: 이 엔터티에 저장해야 하는 변경 내용이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-217">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="c0ab4-218">엔터티는 DB에서 읽을 때 이 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-218">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="c0ab4-219">`Modified`: 일부 또는 모든 엔터티의 속성 값이 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-219">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="c0ab4-220">`SaveChanges` 메서드는 UPDATE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-220">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="c0ab4-221">`Deleted`: 엔터티가 삭제되도록 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-221">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="c0ab4-222">`SaveChanges` 메서드는 DELETE 문을 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-222">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="c0ab4-223">`Detached`: 엔터티가 DB 컨텍스트에서 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-223">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="c0ab4-224">데스크톱 앱에서는 일반적으로 상태 변경 내용이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-224">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="c0ab4-225">엔터티를 읽고 변경이 수행되면, 엔터티 상태가 자동으로 `Modified`로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-225">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="c0ab4-226">`SaveChanges`를 호출하면 변경된 속성만 업데이트하는 SQL UPDATE 문이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-226">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="c0ab4-227">웹앱에서는 페이지를 렌더링한 후 엔터티를 읽고 데이터를 표시하는 `DbContext`가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-227">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="c0ab4-228">페이지 `OnPostAsync` 메서드가 호출되면 `DbContext`의 새 인스턴스와 함께 새 웹 요청이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-228">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="c0ab4-229">새로운 컨텍스트의 엔터티를 다시 읽으면 데스크톱 처리를 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-229">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="c0ab4-230">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="c0ab4-230">Update the Delete page</span></span>

<span data-ttu-id="c0ab4-231">이 섹션에서는 `SaveChanges`에 대한 호출이 실패하는 경우 사용자 지정 오류 메시지를 구현하도록 코드가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-231">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="c0ab4-232">가능한 오류 메시지를 포함하는 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-232">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="c0ab4-233">`OnGetAsync` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-233">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="c0ab4-234">위의 코드는 선택적 매개 변수 `saveChangesError`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-234">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="c0ab4-235">`saveChangesError`는 학생 개체 삭제에 실패한 후 메서드가 호출되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-235">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="c0ab4-236">일시적인 네트워크 문제로 인해 삭제 작업이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-236">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="c0ab4-237">일시적인 네트워크 오류는 클라우드에서 발생 가능성이 더 큽니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-237">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="c0ab4-238">`saveChangesError`는 삭제 페이지 `OnGetAsync`가 UI에서 호출되는 경우 false입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-238">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="c0ab4-239">`OnGetAsync`가 `OnPostAsync`에 의해 호출되면(삭제 작업이 실패했으므로) `saveChangesError` 매개 변수는 true입니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-239">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="c0ab4-240">삭제 페이지 OnPostAsync 메서드</span><span class="sxs-lookup"><span data-stu-id="c0ab4-240">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="c0ab4-241">`OnPostAsync`를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-241">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="c0ab4-242">앞의 코드는 선택한 엔터티를 검색한 다음, [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 메서드를 호출하여 엔터티의 상태를 `Deleted`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-242">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="c0ab4-243">`SaveChanges`가 호출되면 SQL DELETE 명령이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-243">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="c0ab4-244">`Remove`가 실패하는 경우:</span><span class="sxs-lookup"><span data-stu-id="c0ab4-244">If `Remove` fails:</span></span>

* <span data-ttu-id="c0ab4-245">DB 예외가 catch되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-245">The DB exception is caught.</span></span>
* <span data-ttu-id="c0ab4-246">삭제 페이지 `OnGetAsync` 메서드가 `saveChangesError=true`로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-246">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="c0ab4-247">삭제 Razor 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="c0ab4-247">Update the Delete Razor Page</span></span>

<span data-ttu-id="c0ab4-248">다음 강조 표시된 오류 메시지를 삭세 Razor 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-248">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="c0ab4-249">삭제를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-249">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="c0ab4-250">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="c0ab4-250">Common errors</span></span>

<span data-ttu-id="c0ab4-251">Students/Index 또는 다른 링크가 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-251">Students/Index or other links don't work:</span></span>

<span data-ttu-id="c0ab4-252">Razor 페이지에 올바른 `@page` 지시문이 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-252">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="c0ab4-253">예를 들어 Students/Index Razor 페이지는 경로 템플릿을 포함해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-253">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="c0ab4-254">각 Razor 페이지는 `@page` 지시문을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0ab4-254">Each Razor Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="c0ab4-255">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c0ab4-255">Additional resources</span></span>

* [<span data-ttu-id="c0ab4-256">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="c0ab4-256">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="c0ab4-257">[이전](xref:data/ef-rp/intro)
> [다음](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="c0ab4-257">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>
