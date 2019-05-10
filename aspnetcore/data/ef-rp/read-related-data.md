---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 관련 데이터 읽기 - 6/8
author: rick-anderson
description: 이 자습서에서는 관련된 데이터 즉, Entity Framework에서 탐색 속성으로 로드하는 데이터를 읽고 표시합니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e98f6dddc727bb78a411fbd0a5014bcee87c7aeb
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887808"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="7e4e2-103">ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 관련 데이터 읽기 - 6/8</span><span class="sxs-lookup"><span data-stu-id="7e4e2-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="7e4e2-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7e4e2-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="7e4e2-105">이 자습서에서는 관련된 데이터를 읽고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-105">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="7e4e2-106">관련된 데이터는 EF Core에서 탐색 속성에 로드하는 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="7e4e2-107">해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-107">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="7e4e2-108">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-108">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7e4e2-109">다음 그림은 이 자습서에 대해 완료된 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-109">The following illustrations show the completed pages for this tutorial:</span></span>

![과정 인덱스 페이지](read-related-data/_static/courses-index.png)

![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="7e4e2-112">관련된 데이터의 즉시, 명시적 및 지연 로드</span><span class="sxs-lookup"><span data-stu-id="7e4e2-112">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="7e4e2-113">여러 가지 방법으로 EF Core가 관련 데이터를 엔터티의 탐색 속성에 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-113">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="7e4e2-114">[즉시 로드](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-114">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="7e4e2-115">즉시 로드는 한 형식의 엔터티에 대한 쿼리가 관련 엔터티도 로드하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-115">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="7e4e2-116">엔터티를 읽을 때 관련된 데이터가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-116">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="7e4e2-117">이는 일반적으로 필요한 데이터를 모두 검색하는 단일 조인 쿼리를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-117">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="7e4e2-118">EF 코어는 일부 형식의 즉시 로드에 대해 여러 쿼리를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-118">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="7e4e2-119">여러 쿼리를 실행하는 것이 단일 쿼리가 있는 EF6의 일부 쿼리보다 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-119">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="7e4e2-120">즉시 로드는 `Include` 및 `ThenInclude` 메서드로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-120">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![즉시 로드 예제](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="7e4e2-122">즉시 로드는 컬렉션 탐색이 포함된 경우 여러 쿼리를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-122">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="7e4e2-123">주 쿼리에 대해 한 개 쿼리</span><span class="sxs-lookup"><span data-stu-id="7e4e2-123">One query for the main query</span></span> 
  * <span data-ttu-id="7e4e2-124">로드 트리에서 각 컬렉션 "에지"에 대해 한 개 쿼리</span><span class="sxs-lookup"><span data-stu-id="7e4e2-124">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="7e4e2-125">`Load`를 사용한 별도 쿼리: 별도 쿼리로 데이터를 검색할 수 있으며, EF Core에서 탐색 속성을 “수정”합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-125">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="7e4e2-126">"수정"한다는 것은 EF Core가 탐색 속성을 자동으로 채운다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-126">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="7e4e2-127">`Load`로 쿼리를 구분하는 것은 즉시 로드보다 더 명시적인 로드입니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-127">Separate queries with `Load` is more like explict loading than eager loading.</span></span>

  ![별도 쿼리 예제](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="7e4e2-129">참고: EF Core는 이전에 컨텍스트 인스턴스에 로드된 다른 엔터티로 탐색 속성을 자동으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-129">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="7e4e2-130">탐색 속성에 대한 데이터가 명시적으로 포함되지 *않더라도* 관련 엔터티의 일부 또는 전부가 이전에 로드된 경우에도 속성이 채워질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-130">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="7e4e2-131">[명시적 로드](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-131">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="7e4e2-132">엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-132">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7e4e2-133">필요할 때 관련된 데이터를 검색하기 위한 코드를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-133">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="7e4e2-134">별도 쿼리가 있는 명시적 로드의 경우 여러 쿼리가 DB로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-134">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="7e4e2-135">명시적 로드에서 코드는 로드될 탐색 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-135">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="7e4e2-136">`Load` 메서드를 사용하여 명시적 로드를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-136">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="7e4e2-137">예:</span><span class="sxs-lookup"><span data-stu-id="7e4e2-137">For example:</span></span>

  ![명시적 로드 예제](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="7e4e2-139">[지연 로드](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-139">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7e4e2-140">[지연 로드가 버전 2.1의 EF Core에 추가되었습니다](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-140">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7e4e2-141">엔터티를 처음 읽을 때 관련된 데이터가 검색되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-141">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7e4e2-142">탐색 속성에 처음으로 액세스하려고 할 때 해당 탐색 속성에 필요한 데이터가 자동으로 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-142">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="7e4e2-143">탐색 속성에 처음으로 액세스할 때마다 쿼리가 DB에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-143">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="7e4e2-144">`Select` 연산자는 필요한 관련된 데이터만 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-144">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="7e4e2-145">부서 이름을 표시하는 과정 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="7e4e2-145">Create a Course page that displays department name</span></span>

<span data-ttu-id="7e4e2-146">과정 엔터티는 `Department` 엔터티가 포함된 탐색 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-146">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="7e4e2-147">`Department` 엔터티는 과정이 할당된 부서를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-147">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="7e4e2-148">과정 목록에 할당된 부서 이름을 표시하려면</span><span class="sxs-lookup"><span data-stu-id="7e4e2-148">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="7e4e2-149">`Department` 엔터티에서 `Name` 속성을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-149">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="7e4e2-150">`Department` 엔터티는 `Course.Department` 탐색 속성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-150">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="7e4e2-152">과정 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="7e4e2-152">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7e4e2-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e4e2-153">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7e4e2-154">[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-the-student-model)의 지침을 따르고 `Course`를 모델 클래스로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-154">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7e4e2-155">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e4e2-155">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="7e4e2-156">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="7e4e2-157">위의 명령은 `Course` 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-157">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="7e4e2-158">Visual Studio에서 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-158">Open the project in Visual Studio.</span></span>

<span data-ttu-id="7e4e2-159">*Pages/Courses/Index.cshtml.cs*를 열고 `OnGetAsync` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="7e4e2-160">스캐폴딩 엔진은 `Department` 탐색 속성에 대한 즉시 로드를 지정했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="7e4e2-161">`Include` 메서드가 즉시 로드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-161">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="7e4e2-162">앱을 실행하고 **과정** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="7e4e2-163">Department 열에 도움이 되지 않는 `DepartmentID`가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="7e4e2-164">`OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-164">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="7e4e2-165">위의 코드는 `AsNoTracking`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-165">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="7e4e2-166">반환된 엔터티는 추적되지 않으므로 `AsNoTracking`이 성능을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-166">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="7e4e2-167">현재 컨텍스트에서 업데이트되지 않으므로 엔터티가 추적되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-167">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="7e4e2-168">*Pages/Courses/Index.cshtml*을 다음 강조 표시된 내용으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-168">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="7e4e2-169">스캐폴드 코드에 다음 변경 내용을 적용했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-169">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="7e4e2-170">제목을 Index(인덱스)에서 Courses(과정)로 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-170">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="7e4e2-171">`CourseID` 속성 값을 보여 주는 **Number** 열을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-171">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="7e4e2-172">일반적으로 최종 사용자에게 의미가 없으므로 기본적으로 기본 키는 스캐폴드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-172">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="7e4e2-173">그러나 이 경우 기본 키는 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-173">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="7e4e2-174">부서 이름을 표시하도록 **Department** 열을 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-174">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="7e4e2-175">코드는 `Department` 탐색 속성으로 로드되는 `Department` 엔터티의 `Name` 속성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-175">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="7e4e2-176">앱을 실행하고 **Courses(과정)** 탭을 선택하여 부서 이름이 있는 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-176">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![과정 인덱스 페이지](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="7e4e2-178">Select로 관련된 데이터 로드</span><span class="sxs-lookup"><span data-stu-id="7e4e2-178">Loading related data with Select</span></span>

<span data-ttu-id="7e4e2-179">`OnGetAsync` 메서드는 `Include` 메서드로 관련된 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-179">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="7e4e2-180">`Select` 연산자는 필요한 관련된 데이터만 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-180">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="7e4e2-181">`Department.Name`과 같은 단일 항목에서는 SQL INNER JOIN을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-181">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="7e4e2-182">컬렉션의 경우 다른 데이터베이스 액세스를 사용하지만 컬렉션의 `Include` 연산자도 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-182">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="7e4e2-183">다음 코드는 `Select` 메서드로 관련된 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-183">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="7e4e2-184">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="7e4e2-184">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="7e4e2-185">전체 예제는 [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 및 [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-185">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="7e4e2-186">과정 및 등록을 보여 주는 강사 페이지 만들기</span><span class="sxs-lookup"><span data-stu-id="7e4e2-186">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="7e4e2-187">이 섹션에서는 강사 페이지가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-187">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="7e4e2-188">![강사 인덱스 페이지](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="7e4e2-188">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="7e4e2-189">이 페이지는 다음과 같은 방법으로 관련된 데이터를 읽고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-189">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="7e4e2-190">강사 목록은 `OfficeAssignment` 엔터티에서 관련된 데이터를 표시합니다(이전 이미지에서 Office).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-190">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="7e4e2-191">`Instructor` 및 `OfficeAssignment` 엔터티는 일대영 또는 일 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-191">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="7e4e2-192">즉시 로드는 `OfficeAssignment` 엔터티에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-192">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="7e4e2-193">즉시 로드는 일반적으로 관련된 데이터를 표시해야 할 때 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-193">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="7e4e2-194">이 경우 강사를 위한 사무실 할당이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-194">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="7e4e2-195">사용자가 강사(이전 이미지에서 Harui)를 선택하는 경우 관련된 `Course` 엔터티가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-195">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="7e4e2-196">`Instructor` 및 `Course` 엔터티는 다대다 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-196">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="7e4e2-197">`Course` 엔터티 및 관련 `Department` 엔터티에 대해 즉시 로드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-197">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="7e4e2-198">이 경우 선택한 강사에 대한 과정만 필요하므로 별도 쿼리가 더 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-198">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="7e4e2-199">이 예제에서는 탐색 속성에 있는 엔터티에서 탐색 속성에 대한 즉시 로드를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-199">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="7e4e2-200">사용자가 과정(이전 이미지에서 Chemistry)을 선택하면 `Enrollments` 엔터티의 관련된 데이터가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-200">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="7e4e2-201">이전 이미지에서는 학생 이름 및 학점이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-201">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="7e4e2-202">`Course` 및 `Enrollment` 엔터티는 일대다 관계에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-202">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="7e4e2-203">강사 인덱스 뷰에 대한 뷰 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="7e4e2-203">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="7e4e2-204">강사 페이지는 서로 다른 세 테이블의 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-204">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="7e4e2-205">세 개 테이블을 나타내는 세 개 엔터티가 포함된 뷰 모델이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-205">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="7e4e2-206">다음 코드로 *SchoolViewModels* 폴더에서 *InstructorIndexData.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-206">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="7e4e2-207">강사 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="7e4e2-207">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7e4e2-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e4e2-208">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7e4e2-209">[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-the-student-model)의 지침을 따르고 `Instructor`를 모델 클래스로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-209">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7e4e2-210">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e4e2-210">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="7e4e2-211">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-211">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="7e4e2-212">위의 명령은 `Instructor` 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-212">The preceding command scaffolds the `Instructor` model.</span></span> <span data-ttu-id="7e4e2-213">앱을 실행하고 강사 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-213">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="7e4e2-214">*Pages/Instructors/Index.cshtml.cs*를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-214">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="7e4e2-215">`OnGetAsync` 메서드는 선택한 강사의 ID에 대해 경로 데이터(선택 사항)를 받아들입니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-215">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="7e4e2-216">*Pages/Instructors/Index.cshtml.cs* 파일에서 쿼리를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-216">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="7e4e2-217">쿼리에는 다음 두 include가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-217">The query has two includes:</span></span>

* <span data-ttu-id="7e4e2-218">`OfficeAssignment`: [강사 뷰](#IP)에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-218">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="7e4e2-219">`CourseAssignments`: 가르친 과정을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-219">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="7e4e2-220">강사 인덱스 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e4e2-220">Update the instructors Index page</span></span>

<span data-ttu-id="7e4e2-221">*Pages/Instructors/Index.cshtml*을 다음 표시로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-221">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="7e4e2-222">위의 표시로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-222">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="7e4e2-223">`page` 지시문을 `@page`에서 `@page "{id:int?}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-223">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="7e4e2-224">`"{id:int?}"`는 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-224">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="7e4e2-225">경로 템플릿은 데이터를 라우팅할 URL에서 정수 쿼리 문자열을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-225">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="7e4e2-226">예를 들어, `@page` 지시문만 있는 강사에 대해 **Select** 링크를 클릭하면 다음과 같은 URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-226">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="7e4e2-227">page 지시문이 `@page "{id:int?}"`이면, 이전 URL은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-227">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="7e4e2-228">페이지 제목은 **Instructors(강사)** 입니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-228">Page title is **Instructors**.</span></span>
* <span data-ttu-id="7e4e2-229">`item.OfficeAssignment`가 Null이 아닌 경우에만 `item.OfficeAssignment.Location`을 표시하는 **Office** 열을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-229">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="7e4e2-230">이는 일대영 또는 일 관계이기 때문에 관련된 OfficeAssignment 엔터티가 있을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-230">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="7e4e2-231">각 강사가 가르치는 과정을 표시하는 **Courses** 열을 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-231">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="7e4e2-232">이 razor 구문에 대한 자세한 내용은 [`@:`를 사용하여 명시적 줄 전환](xref:mvc/views/razor#explicit-line-transition-with-)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-232">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="7e4e2-233">선택된 강사의 `tr` 요소에 `class="success"`를 동적으로 추가하는 코드를 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-233">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="7e4e2-234">부트스트랩 클래스를 사용하여 선택된 행에 대한 배경색을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-234">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="7e4e2-235">**Select**로 레이블 지정된 새 하이퍼링크를 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-235">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="7e4e2-236">이 링크는 선택한 강사의 ID를 `Index` 메서드에 보내고 배경색을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-236">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="7e4e2-237">앱을 실행하고 **강사** 탭을 선택합니다. 페이지에 관련된 `OfficeAssignment` 엔터티의 `Location`(사무실)이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-237">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="7e4e2-238">OfficeAssignment가 Null이면 빈 테이블 셀이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-238">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

![선택한 항목이 없는 강사 인덱스 페이지](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="7e4e2-240">**Select** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-240">Click on the **Select** link.</span></span> <span data-ttu-id="7e4e2-241">행 스타일이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-241">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="7e4e2-242">선택한 강사가 가르친 과정 추가</span><span class="sxs-lookup"><span data-stu-id="7e4e2-242">Add courses taught by selected instructor</span></span>

<span data-ttu-id="7e4e2-243">*Pages/Instructors/Index.cshtml.cs*에서 `OnGetAsync` 메서드를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-243">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="7e4e2-244">`public int CourseID { get; set; }` 추가</span><span class="sxs-lookup"><span data-stu-id="7e4e2-244">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="7e4e2-245">업데이트된 쿼리를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-245">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="7e4e2-246">이전 쿼리는 `Department` 엔터티를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-246">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="7e4e2-247">다음 코드는 강사가 선택되었을 때 실행됩니다(`id != null`).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-247">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="7e4e2-248">선택한 강사는 뷰 모델의 강사 목록에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-248">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="7e4e2-249">뷰 모델의 `Courses` 속성은 해당 강사의 `CourseAssignments` 탐색 속성에서 `Course` 엔터티로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-249">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="7e4e2-250">`Where` 메서드는 컬렉션도 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-250">The `Where` method returns a collection.</span></span> <span data-ttu-id="7e4e2-251">앞의 `Where` 메서드에서 단일 `Instructor` 엔터티만 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-251">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="7e4e2-252">`Single` 메서드는 컬렉션을 단일 `Instructor` 엔터티로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-252">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="7e4e2-253">`Instructor` 엔터티는 `CourseAssignments` 속성에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-253">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="7e4e2-254">`CourseAssignments`는 관련 `Course` 엔터티에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-254">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![강사 및 과정 간 관계 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="7e4e2-256">`Single` 메서드는 컬렉션에 한 개 항목만 있을 때 컬렉션에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-256">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="7e4e2-257">`Single` 메서드는 컬렉션이 비어 있거나 둘 이상의 항목이 있는 경우 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-257">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="7e4e2-258">대안은 `SingleOrDefault`입니다. 컬렉션이 비어 있는 경우 기본값을 반환합니다(이 경우 Null).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-258">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="7e4e2-259">비어 있는 컬렉션에 `SingleOrDefault` 사용</span><span class="sxs-lookup"><span data-stu-id="7e4e2-259">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="7e4e2-260">예외가 발생합니다(null 참조에서 `Courses` 속성을 찾으려고 시도하므로).</span><span class="sxs-lookup"><span data-stu-id="7e4e2-260">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="7e4e2-261">예외 메시지로는 문제의 원인을 명확하게 알기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-261">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="7e4e2-262">다음 코드는 과정을 선택할 때 뷰 모델의 `Enrollments` 속성을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-262">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="7e4e2-263">다음 태그를 *Pages/Instructors/Index.cshtml* Razor Page 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-263">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="7e4e2-264">위의 표시는 강사가 선택된 경우 강사와 관련된 과정의 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-264">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="7e4e2-265">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-265">Test the app.</span></span> <span data-ttu-id="7e4e2-266">강사 페이지에서 **Select** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-266">Click on a **Select** link on the instructors page.</span></span>

![강사가 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a><span data-ttu-id="7e4e2-268">학생 데이터 표시</span><span class="sxs-lookup"><span data-stu-id="7e4e2-268">Show student data</span></span>

<span data-ttu-id="7e4e2-269">이 섹션에서는 선택한 과정에 대한 학생 데이터를 표시하도록 앱이 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-269">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="7e4e2-270">*Pages/Instructors/Index.cshtml.cs*에서 `OnGetAsync` 메서드의 쿼리를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-270">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="7e4e2-271">*Pages/Instructors/Index.cshtml*을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-271">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="7e4e2-272">다음 표시를 파일 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-272">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="7e4e2-273">이전 표시는 선택한 과정을 등록한 학생의 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-273">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="7e4e2-274">페이지를 새로 고치고 강사를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-274">Refresh the page and select an instructor.</span></span> <span data-ttu-id="7e4e2-275">과정을 선택하여 등록된 학생 및 해당 등급의 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-275">Select a course to see the list of enrolled students and their grades.</span></span>

![강사 및 과정이 선택된 강사 인덱스 페이지](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="7e4e2-277">Single 사용</span><span class="sxs-lookup"><span data-stu-id="7e4e2-277">Using Single</span></span>

<span data-ttu-id="7e4e2-278">`Single` 메서드는 `Where` 메서드를 별도로 호출하는 대신 `Where` 조건을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-278">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="7e4e2-279">위의 `Single` 접근 방식은 `Where`를 사용하는 것보다 장점을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-279">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="7e4e2-280">일부 개발자는 `Single` 방식을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-280">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="7e4e2-281">명시적 로드</span><span class="sxs-lookup"><span data-stu-id="7e4e2-281">Explicit loading</span></span>

<span data-ttu-id="7e4e2-282">현재 코드는 `Enrollments` 및 `Students`에 대한 즉시 로드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-282">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="7e4e2-283">사용자가 과정에 등록된 내용을 거의 보지 않는다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-283">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="7e4e2-284">이 경우 최적화는 요청된 경우에만 등록 데이터를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-284">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="7e4e2-285">이 섹션에서는 `Enrollments` 및 `Students`의 명시적 로드를 사용하도록 `OnGetAsync`가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-285">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="7e4e2-286">`OnGetAsync`를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-286">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="7e4e2-287">위의 코드는 등록 및 학생 데이터에 대한 *ThenInclude* 메서드 호출을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-287">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="7e4e2-288">과정을 선택하면 강조 표시된 코드가 다음을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-288">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="7e4e2-289">선택한 과정에 대한 `Enrollment` 엔터티</span><span class="sxs-lookup"><span data-stu-id="7e4e2-289">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="7e4e2-290">각 `Enrollment`에 대한 `Student` 엔터티</span><span class="sxs-lookup"><span data-stu-id="7e4e2-290">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="7e4e2-291">위의 코드에서는 `.AsNoTracking()`을 주석 처리했습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-291">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="7e4e2-292">탐색 속성은 추적된 엔터티에 대해서만 명시적으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-292">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="7e4e2-293">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-293">Test the app.</span></span> <span data-ttu-id="7e4e2-294">사용자 관점에서 앱은 이전 버전과 동일하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-294">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="7e4e2-295">다음 자습서에서는 관련된 데이터를 업데이트하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e4e2-295">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e4e2-296">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7e4e2-296">Additional resources</span></span>

* [<span data-ttu-id="7e4e2-297">이 자습서의 YouTube 버전(1부)</span><span class="sxs-lookup"><span data-stu-id="7e4e2-297">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="7e4e2-298">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="7e4e2-298">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="7e4e2-299">[이전](xref:data/ef-rp/complex-data-model)
>[다음](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="7e4e2-299">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>
