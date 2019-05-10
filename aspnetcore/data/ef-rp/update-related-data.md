---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 관련 데이터 업데이트 - 7/8
author: rick-anderson
description: 이 자습서에서는 외래 키 필드 및 탐색 속성을 업데이트하여 관련된 데이터를 업데이트합니다.
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: b996e7f9c8e422060f6378a6004a53906e2af7c7
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087091"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="66031-103">ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 관련 데이터 업데이트 - 7/8</span><span class="sxs-lookup"><span data-stu-id="66031-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="66031-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66031-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="66031-105">이 자습서에서는 관련된 데이터 업데이트를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="66031-105">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="66031-106">해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="66031-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="66031-107">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="66031-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="66031-108">다음 그림은 완료된 페이지의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="66031-108">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="66031-109">![강좌 편집 페이지](update-related-data/_static/course-edit.png)
![강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="66031-109">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="66031-110">만들기 및 편집 강좌 페이지를 검사하고 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-110">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="66031-111">새 강좌를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-111">Create a new course.</span></span> <span data-ttu-id="66031-112">부서는 해당 이름이 아닌 해당 기본 키(정수)로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-112">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="66031-113">새 강좌를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-113">Edit the new course.</span></span> <span data-ttu-id="66031-114">테스트를 완료하면 새 강좌를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-114">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="66031-115">공통 코드를 공유하는 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="66031-115">Create a base class to share common code</span></span>

<span data-ttu-id="66031-116">강좌/만들기 및 강좌/페이지 편집 각각은 부서 이름의 목록이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-116">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="66031-117">만들기 및 편집 페이지에 대해 *Pages/Courses/DepartmentNamePageModel.cshtml.cs* 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-117">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="66031-118">위의 코드에서는 부서 이름의 목록을 포함하도록 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-118">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="66031-119">`selectedDepartment`가 지정된 경우 해당 부서는 `SelectList`에서 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-119">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="66031-120">만들기 및 편집 페이지 모델 클래스는 `DepartmentNamePageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-120">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="66031-121">강좌 페이지 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="66031-121">Customize the Courses Pages</span></span>

<span data-ttu-id="66031-122">새 강좌 엔터티가 만들어질 때 기존 부서에 대한 관계가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-122">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="66031-123">강좌를 만드는 동안 부서를 추가하기 위해 만들기 및 편집에 대한 기본 클래스는 부서를 선택하기 위한 드롭다운 목록을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-123">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="66031-124">드롭다운 목록은 `Course.DepartmentID` FK(외래 키) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-124">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="66031-125">EF Core는 `Course.DepartmentID` FK를 사용하여 `Department` 탐색 속성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-125">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![강좌 만들기](update-related-data/_static/ddl.png)

<span data-ttu-id="66031-127">다음 코드로 만들기 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-127">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="66031-128">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="66031-128">The preceding code:</span></span>

* <span data-ttu-id="66031-129">`DepartmentNamePageModel`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-129">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="66031-130">[초과 게시](xref:data/ef-rp/crud#overposting)를 방지하도록 `TryUpdateModelAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-130">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="66031-131">`ViewData["DepartmentID"]`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="66031-131">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="66031-132">`ViewData["DepartmentID"]`는 강력한 형식의 `DepartmentNameSL`로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-132">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="66031-133">강력한 형식의 모델은 약한 형식보다 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-133">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="66031-134">자세한 내용은 [약한 형식의 데이터(ViewData 및 ViewBag)](xref:mvc/views/overview#VD_VB)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66031-134">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="66031-135">강좌 만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="66031-135">Update the Courses Create page</span></span>

<span data-ttu-id="66031-136">다음 표시로 *Pages/Courses/Create.cshtml*을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-136">Update *Pages/Courses/Create.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="66031-137">위의 표시로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-137">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="66031-138">캡션을 **DepartmentID**에서 **Department**로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-138">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="66031-139">`"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="66031-139">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="66031-140">"부서 선택" 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-140">Adds the "Select Department" option.</span></span> <span data-ttu-id="66031-141">이 변경 내용은 첫 번째 부서 대신 "부서 선택"을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-141">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="66031-142">부서가 선택되지 않은 경우 유효성 검사 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-142">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="66031-143">Razor 페이지는 [Select 태그 도우미](xref:mvc/views/working-with-forms#the-select-tag-helper)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-143">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="66031-144">만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-144">Test the Create page.</span></span> <span data-ttu-id="66031-145">만들기 페이지는 부서 ID보다는 부서 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-145">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="66031-146">강좌 만들기 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-146">Update the Courses Edit page.</span></span>

<span data-ttu-id="66031-147">다음 코드로 편집 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-147">Update the edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="66031-148">변경 내용은 만들기 페이지 모델에서 만든 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-148">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="66031-149">위의 코드에서 `PopulateDepartmentsDropDownList`는 드롭다운 목록에 지정된 부서를 선택하는 부서 ID를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-149">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="66031-150">다음 표시로 *Pages/Courses/Edit.cshtml*을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-150">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="66031-151">위의 표시로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-151">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="66031-152">강좌 ID를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-152">Displays the course ID.</span></span> <span data-ttu-id="66031-153">일반적으로 엔터티의 PK(기본 키)는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-153">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="66031-154">PK는 일반적으로 사용자에게 아무런 의미가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-154">PKs are usually meaningless to users.</span></span> <span data-ttu-id="66031-155">이 경우 PK는 강좌 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="66031-155">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="66031-156">캡션을 **DepartmentID**에서 **Department**로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-156">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="66031-157">`"ViewBag.DepartmentID"`를 `DepartmentNameSL`로 바꿉니다(기본 클래스에서).</span><span class="sxs-lookup"><span data-stu-id="66031-157">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="66031-158">페이지는 강좌 번호에 대한 숨겨진 필드(`<input type="hidden">`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-158">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="66031-159">`asp-for="Course.CourseID"`로 `<label>` 태그 도우미를 추가하는 것은 숨겨진 필드에 대한 필요성을 제거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-159">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="66031-160">`<input type="hidden">`은 사용자가 **저장**을 클릭할 때 게시된 데이터에 포함되도록 강좌 번호에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-160">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="66031-161">업데이트된 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-161">Test the updated code.</span></span> <span data-ttu-id="66031-162">강좌를 만들고, 편집하고, 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-162">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="66031-163">세부 정보 및 삭제 페이지 모델에 AsNoTracking 추가</span><span class="sxs-lookup"><span data-stu-id="66031-163">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="66031-164">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__)은 추적이 필요하지 않은 경우 성능을 향상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-164">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="66031-165">삭제 및 세부 정보 페이지 모델에 `AsNoTracking`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-165">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="66031-166">다음 코드에서는 업데이트된 삭제 페이지 모델을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="66031-166">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="66031-167">*Pages/Courses/Details.cshtml.cs* 파일에서 `OnGetAsync` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-167">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="66031-168">삭제 및 세부 정보 페이지 수정</span><span class="sxs-lookup"><span data-stu-id="66031-168">Modify the Delete and Details pages</span></span>

<span data-ttu-id="66031-169">다음 표시로 삭제 Razor 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-169">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="66031-170">세부 정보 페이지에 동일한 변경 내용을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-170">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="66031-171">강좌 페이지 테스트</span><span class="sxs-lookup"><span data-stu-id="66031-171">Test the Course pages</span></span>

<span data-ttu-id="66031-172">만들기, 편집, 세부 정보 및 삭제를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-172">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="66031-173">강사 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="66031-173">Update the instructor pages</span></span>

<span data-ttu-id="66031-174">다음 섹션에서는 강사 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-174">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="66031-175">사무실 위치 추가</span><span class="sxs-lookup"><span data-stu-id="66031-175">Add office location</span></span>

<span data-ttu-id="66031-176">강사 레코드를 편집할 때 강사의 사무실 할당을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-176">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="66031-177">`Instructor` 엔터티에는 `OfficeAssignment` 엔터티와 일대영 또는 일 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-177">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="66031-178">강사 코드를 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-178">The instructor code must handle:</span></span>

* <span data-ttu-id="66031-179">사용자가 사무실 할당을 해제하는 경우 `OfficeAssignment` 엔터티를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-179">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="66031-180">사용자가 사무실 할당을 입력하고 비어 있던 경우 새 `OfficeAssignment` 엔터티를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-180">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="66031-181">사용자가 사무실 할당을 변경하는 경우 `OfficeAssignment` 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-181">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="66031-182">다음 코드로 강사 편집 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-182">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="66031-183">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="66031-183">The preceding code:</span></span>

* <span data-ttu-id="66031-184">`OfficeAssignment` 탐색 속성에 대한 즉시 로드를 사용하여 데이터베이스에서 현재 `Instructor` 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="66031-184">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="66031-185">모델 바인더의 값으로 검색된 `Instructor` 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-185">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="66031-186">`TryUpdateModel`은 [초과 게시](xref:data/ef-rp/crud#overposting)를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-186">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="66031-187">사무실 위치가 비어 있는 경우 `Instructor.OfficeAssignment`를 Null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-187">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="66031-188">`Instructor.OfficeAssignment`가 Null인 경우 `OfficeAssignment` 테이블의 관련된 행이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-188">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="66031-189">강사 편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="66031-189">Update the instructor Edit page</span></span>

<span data-ttu-id="66031-190">*Pages/Instructors/Edit.cshtml*을 사무실 위치로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-190">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="66031-191">강사 사무실 위치를 변경할 수 있음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-191">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="66031-192">강사 편집 페이지에 강좌 할당 추가</span><span class="sxs-lookup"><span data-stu-id="66031-192">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="66031-193">강사는 강좌 수에 관계 없이 가르칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-193">Instructors may teach any number of courses.</span></span> <span data-ttu-id="66031-194">이 섹션에서는 강좌 할당을 변경하는 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-194">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="66031-195">다음 그림에서는 업데이트된 강사 편집 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="66031-195">The following image shows the updated instructor Edit page:</span></span>

![강좌가 있는 강사 편집 페이지](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="66031-197">`Course` 및 `Instructor`에는 다대다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-197">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="66031-198">관계를 추가하고 제거하려면 `CourseAssignments` 조인 엔터티 집합에서 엔터티를 추가하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-198">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="66031-199">확인란은 강사에게 할당된 강좌에 대한 변경 내용을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-199">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="66031-200">데이터베이스의 모든 강좌에 대해 확인란이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-200">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="66031-201">강사에게 할당된 강좌가 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-201">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="66031-202">사용자는 확인란을 선택하거나 선택 취소하여 강좌 할당을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-202">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="66031-203">강좌의 번호가 훨씬 더 큰 경우:</span><span class="sxs-lookup"><span data-stu-id="66031-203">If the number of courses were much greater:</span></span>

* <span data-ttu-id="66031-204">아마도 강좌를 표시하는 데 다른 사용자 인터페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-204">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="66031-205">관계를 만들거나 삭제하는 조인 엔터티를 조작하는 방법은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-205">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="66031-206">만들기 및 편집 강사 페이지를 지원하는 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="66031-206">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="66031-207">다음 코드로 *SchoolViewModels/AssignedCourseData.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-207">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="66031-208">`AssignedCourseData` 클래스는 강사별 할당된 강좌에 대한 확인란을 만드는 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-208">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="66031-209">*Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-209">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="66031-210">`InstructorCoursesPageModel`은 편집 및 만들기 페이지 모델에 사용하는 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="66031-210">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="66031-211">`PopulateAssignedCourseData`는 `AssignedCourseDataList`를 채우도록 모든 `Course` 엔터티를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-211">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="66031-212">각 강좌의 경우 코드는 `CourseID`, 제목 및 강사가 강좌에 할당되었는지 여부를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-212">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="66031-213">[HashSet](/dotnet/api/system.collections.generic.hashset-1)는 효율적인 조회를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-213">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="66031-214">강사 편집 페이지 모델</span><span class="sxs-lookup"><span data-stu-id="66031-214">Instructors Edit page model</span></span>

<span data-ttu-id="66031-215">다음 코드로 강사 편집 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-215">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="66031-216">위의 코드는 사무실 할당 변경을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-216">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="66031-217">강사 Razor 보기를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-217">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="66031-218">Visual Studio에서 코드를 붙여 넣을 때 줄 바꿈이 코드를 중단하는 방식으로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-218">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="66031-219">자동 서식 지정을 실행 취소하려면 Ctrl+Z를 한 번 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="66031-219">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="66031-220">Ctrl+Z는 여기와 같은 모양이 되도록 줄 바꿈을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-220">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="66031-221">들여쓰기는 완벽할 필요가 없지만 `@</tr><tr>`, `@:<td>`, `@:</td>` 및 `@:</tr>` 줄은 표시된 것처럼 각각 한 줄에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-221">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="66031-222">선택된 새 코드의 블록과 함께 Tab 키를 세 번 눌러 기존 코드와 함께 새 코드를 정렬합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-222">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="66031-223">[이 링크를 통해](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html) 이 버그의 상태를 투표하거나 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-223">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="66031-224">위의 코드는 세 개의 열이 있는 HTML 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="66031-224">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="66031-225">각 열에는 확인란과 강좌 번호 및 제목을 포함하는 캡션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-225">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="66031-226">모든 확인란에는 동일한 이름("selectedCourses")이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-226">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="66031-227">동일한 이름을 사용하면 모델 바인더에 그룹으로 취급하도록 알립니다.</span><span class="sxs-lookup"><span data-stu-id="66031-227">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="66031-228">각 확인란의 값 특성은 `CourseID`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-228">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="66031-229">페이지가 게시되면 모델 바인더는 선택된 확인란에 대한 `CourseID` 값으로 구성된 배열을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-229">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="66031-230">확인란이 처음 렌더링되는 경우 강사에게 할당된 강좌는 특성을 확인했습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-230">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="66031-231">앱을 실행하고 업데이트된 강사 편집 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-231">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="66031-232">일부 강좌 할당을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-232">Change some course assignments.</span></span> <span data-ttu-id="66031-233">변경 내용은 인덱스 페이지에 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-233">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="66031-234">참고: 강사 강좌 데이터를 편집하기 위해 여기에 적용되는 방법은 제한된 수의 강좌가 있는 경우에 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-234">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="66031-235">훨씬 큰 컬렉션의 경우 다른 UI 및 다른 업데이트 메서드는 더욱 사용 가능하며 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="66031-235">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="66031-236">강사 만들기 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="66031-236">Update the instructors Create page</span></span>

<span data-ttu-id="66031-237">다음 코드로 강사 만들기 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-237">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="66031-238">위의 코드는 *Pages/Instructors/Edit.cshtml.cs* 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-238">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="66031-239">다음 표시로 강사 만들기 Razor 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-239">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="66031-240">강사 만들기 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-240">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="66031-241">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="66031-241">Update the Delete page</span></span>

<span data-ttu-id="66031-242">다음 코드로 삭제 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-242">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="66031-243">위의 코드로 다음이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="66031-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="66031-244">`CourseAssignments` 탐색 속성에 대해 즉시 로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-244">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="66031-245">`CourseAssignments`는 포함되어야 합니다. 또는 강사가 삭제될 때 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="66031-245">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="66031-246">읽을 필요가 없도록 하려면 데이터베이스에 계단식 삭제를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-246">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="66031-247">삭제될 강사가 부서의 관리자로 할당된 경우 해당 부서에서 강사 할당을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="66031-247">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66031-248">추가 자료</span><span class="sxs-lookup"><span data-stu-id="66031-248">Additional resources</span></span>

* [<span data-ttu-id="66031-249">이 자습서의 YouTube 버전(1부)</span><span class="sxs-lookup"><span data-stu-id="66031-249">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="66031-250">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="66031-250">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="66031-251">[이전](xref:data/ef-rp/read-related-data)
> [다음](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="66031-251">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>
