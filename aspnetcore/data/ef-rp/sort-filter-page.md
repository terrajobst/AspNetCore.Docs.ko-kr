---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 정렬, 필터, 페이징 - 3/8
author: rick-anderson
description: 이 자습서에서는 ASP.NET Core 및 Entity Framework Core를 사용하여 페이지에 정렬, 필터링 및 페이징 기능을 추가합니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 228722ecdc0fb19e024b7154393390fe6d34c5ff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887308"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 정렬, 필터, 페이징 - 3/8

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

작성자: [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

이 자습서에서는 정렬, 필터링, 그룹화 및 페이징 기능이 추가됩니다.

다음 그림은 완료된 페이지를 보여 줍니다. 열 제목은 열을 정렬하는 클릭할 수 있는 링크입니다. 열 제목을 반복적으로 클릭하면 오름차순과 내림차순 정렬 순서 사이로 전환됩니다.

![학생 인덱스 페이지](sort-filter-page/_static/paging.png)

해결할 수 없는 문제가 발생한 경우 [완성된 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)을 다운로드합니다.

## <a name="add-sorting-to-the-index-page"></a>인덱스 페이지에 정렬 추가

정렬 매개 변수를 포함하도록 문자열을 *Students/Index.cshtml.cs* `PageModel`에 추가합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

다음 코드로 *Students/Index.cshtml.cs* `OnGetAsync`를 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

이전 코드는 URL의 쿼리 문자열에서 `sortOrder` 매개 변수를 받습니다. URL(쿼리 문자열 포함)이 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)에서 생성됩니다.

`sortOrder` 매개 변수는 “Name” 또는 “Date”입니다. `sortOrder` 매개 변수의 뒤에 오는 “_desc”는 내림차순 순서를 지정하기 위한 옵션입니다. 기본 정렬 순서는 오름차순입니다.

인덱스 페이지가 **학생** 링크에서 요청되는 경우 쿼리 문자열이 없습니다. 학생은 성 기준 오름차순으로 표시됩니다. `switch` 문에서 성 기준 오름차순이 기본값(제어 이동 사례)입니다. 사용자가 열 제목 링크를 클릭하면 적절한 `sortOrder` 값이 쿼리 문자열 값에 제공됩니다.

열 제목 하이퍼링크를 적절한 쿼리 문자열 값으로 구성하기 위해 `NameSort` 및 `DateSort`가 사용됩니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

다음 코드는 C# 조건적 [?: 연산자](/dotnet/csharp/language-reference/operators/conditional-operator)를 포함합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

첫 번째 줄은 `sortOrder`가 null이거나 비어 있는 경우 `NameSort`가 “name_desc”로 설정되도록 지정합니다. `sortOrder`가 null 또는 비어 있지 **않은** 경우 `NameSort`는 빈 문자열로 설정됩니다.

`?: operator`는 삼진 연산자라고도 합니다.

이러한 두 명령문을 사용하면 페이지에서 다음과 같이 열 제목 하이퍼링크를 설정할 수 있습니다.

| 현재 정렬 순서 | 성 하이퍼링크 | 날짜 하이퍼링크 |
|:--------------------:|:-------------------:|:--------------:|
| 성 오름차순 | descending        | ascending      |
| 성 내림차순 | ascending           | ascending      |
| 날짜 오름차순       | ascending           | descending     |
| 날짜 내림차순      | ascending           | ascending      |

메서드는 LINQ to Entities를 사용하여 정렬할 기준이 되는 열을 지정합니다. 코드는 `IQueryable<Student>`를 switch 문 앞에서 초기화하고 switch 문에서 수정합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 `IQueryable`을 만들거나 수정하는 경우 데이터베이스에 쿼리가 전송되지 않습니다. 쿼리는 `IQueryable` 개체가 컬렉션으로 변환될 때까지 실행되지 않습니다. `ToListAsync`와 같은 메서드를 호출하면 `IQueryable`이 컬렉션으로 변환됩니다. 따라서 `IQueryable` 코드는 다음 명령문까지 실행되지 않는 단일 쿼리가 됩니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`는 많은 수의 정렬 가능한 열이 포함된 자세한 정보를 가져올 수 있습니다.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>학생 인덱스 페이지에 열 제목 하이퍼링크 추가

*Students/Index.cshtml*의 코드를 다음 강조 표시된 코드로 바꿉니다.

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

위의 코드는:

* 하이퍼링크를 `LastName` 및 `EnrollmentDate` 열 머리글에 추가합니다.
* `NameSort` 및 `DateSort`의 정보를 사용하여 현재 정렬 순서 값으로 하이퍼링크를 설정합니다.

정렬이 작동하는지 확인하려면 다음을 수행합니다.

* 앱을 실행하고 **학생** 탭을 선택합니다.
* **성**을 클릭합니다.
* **등록 날짜**를 클릭합니다.

코드를 더 잘 이해하려면 다음을 수행합니다.

* *Students/Index.cshtml.cs*에서 `switch (sortOrder)`에 중단점을 설정합니다.
* `NameSort` 및 `DateSort`에 대한 조사식을 추가합니다.
* *Students/Index.cshtml*에서 `@Html.DisplayNameFor(model => model.Student[0].LastName)`에 중단점을 설정합니다.

디버거를 단계별로 실행합니다.

## <a name="add-a-search-box-to-the-students-index-page"></a>학생 인덱스 페이지에 검색 상자 추가

학생 인덱스 페이지에 필터링을 추가하려면 다음을 수행합니다.

* 텍스트 상자 및 전송 단추가 Razor 페이지에 추가됩니다. 텍스트 상자는 첫 번째 또는 마지막 이름에 검색 문자열을 제공합니다.
* 페이지 모델이 텍스트 상자 값을 사용하도록 업데이트됩니다.

### <a name="add-filtering-functionality-to-the-index-method"></a>인덱스 메서드에 필터링 기능 추가

다음 코드로 *Students/Index.cshtml.cs* `OnGetAsync`를 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

위의 코드는:

* `searchString` 매개 변수를 `OnGetAsync` 메서드에 추가합니다. 다음 섹션에서 추가되는 텍스트 상자에서 검색 문자열 값이 수신됩니다.
* LINQ 문 및 `Where` 절을 추가했습니다. `Where` 절은 이름 또는 성에 검색 문자열이 포함된 학생만 선택합니다. LINQ 문은 검색할 값이 있는 경우에만 실행됩니다.

참고: 이전 코드는 `IQueryable` 개체에서 `Where` 메서드를 호출하고, 필터는 서버에서 처리됩니다. 일부 시나리오에서 앱은 메모리 내 컬렉션에서 확장 메서드로 `Where` 메서드를 호출할 수 있습니다. 예를 들어 `_context.Students`가 EF Core `DbSet`에서 `IEnumerable` 컬렉션을 반환하는 리포지토리 메서드로 변경되었다고 가정합니다. 결과는 일반적으로 동일하지만 경우에 따라 다를 수 있습니다.

예를 들어 `Contains`의 .NET Framework 구현은 기본적으로 대/소문자 구분 비교를 수행합니다. SQL Server에서 `Contains` 대/소문자 구분은 SQL Server 인스턴스의 컬렉션 설정에 의해 결정됩니다. SQL Server는 기본적으로 대/소문자를 구분하지 않습니다. `ToUpper`는 테스트가 명시적으로 대/소문자를 구분하지 않도록 하기 위해 호출됩니다.

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

위의 코드는 코드가 `IEnumerable`을 사용하도록 변경된 경우 결과가 대/소문자를 구분하지 않는지 확인합니다. `Contains`가 `IEnumerable` 컬렉션에서 호출된 경우 .NET Core 구현이 사용됩니다. `Contains`가 `IQueryable` 개체에서 호출된 경우 데이터베이스 구현이 사용됩니다. 리포지토리에서 `IEnumerable`을 반환하면 상당한 성능 저하가 발생할 수 있습니다.

1. 모든 행이 DB 서버에서 반환됩니다.
1. 필터는 애플리케이션에서 반환된 모든 행에 적용됩니다.

`ToUpper` 호출에 대한 성능 저하가 발생합니다. `ToUpper` 코드는 TSQL SELECT 문의 WHERE 절에 함수를 추가합니다. 추가된 함수는 최적화 프로그램이 인덱스를 사용하지 않도록 합니다. SQL은 대/소문자를 구분하지 않도록 설치되어 있으므로 필요하지 않은 경우 `ToUpper` 호출을 피하는 것이 가장 좋습니다.

### <a name="add-a-search-box-to-the-student-index-page"></a>학생 인덱스 페이지에 검색 상자 추가

*Pages/Students/Index.cshtml*에서 다음 강조 표시된 코드를 추가하여 **검색** 단추와 다양한 크롬을 만듭니다.

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

이전 코드는 `<form>` [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하여 검색 텍스트 상자 및 단추를 추가합니다. 기본적으로 `<form>` 태그 도우미는 POST로 양식 데이터를 전송합니다. POST를 사용하면 매개 변수가 URL에 없는 HTTP 메시지 본문에 전달됩니다. HTTP GET을 사용하는 경우 양식 데이터가 URL에 쿼리 문자열로 전달됩니다. 사용자는 쿼리 문자열로 데이터를 전달하여 URL을 책갈피로 표시할 수 있습니다. [W3C 지침](https://www.w3.org/2001/tag/doc/whenToUseGet.html)에 따라 작업이 업데이트되지 않을 때 GET을 사용해야 합니다.

앱을 테스트합니다.

* **학생** 탭을 선택하고 검색 문자열을 입력합니다.
* **검색**을 선택합니다.

URL에 검색 문자열이 포함되어 있음을 확인하세요.

```html
http://localhost:5000/Students?SearchString=an
```

페이지가 책갈피로 표시된 경우 책갈피에는 페이지에 대한 URL 및 `SearchString` 쿼리 문자열이 포함됩니다. `form` 태그의 `method="get"`으로 인해 쿼리 문자열이 생성됩니다.

현재 열 제목 정렬 링크를 선택하면 **검색** 상자에서 필터 값이 손실됩니다. 손실된 필터 값은 다음 섹션에서 수정됩니다.

## <a name="add-paging-functionality-to-the-students-index-page"></a>학생 인덱스 페이지에 페이징 기능 추가

이 섹션에서는 페이징을 지원하기 위해 `PaginatedList` 클래스를 만듭니다. `PaginatedList` 클래스는 `Skip` 및 `Take` 문을 사용하여 테이블의 모든 행을 검색하는 대신 서버에 있는 데이터를 필터링합니다. 다음 그림에는 페이징 단추가 나와 있습니다.

![페이징 링크가 있는 학생 인덱스 페이지](sort-filter-page/_static/paging.png)

프로젝트 폴더에서 다음 코드로 `PaginatedList.cs`를 만듭니다.

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

이전 코드에서 `CreateAsync` 메서드는 페이지 크기 및 페이지 번호를 사용하고 적절한 `Skip` 및 `Take` 문을 `IQueryable`에 적용합니다. `IQueryable`에서 `ToListAsync`를 호출하면 요청된 페이지만 포함하는 목록을 반환합니다. **이전** 및 **다음** 페이징 단추를 사용 또는 사용하지 않도록 설정하는 데 `HasPreviousPage` 및 `HasNextPage` 속성을 사용합니다.

`CreateAsync` 메서드는 `PaginatedList<T>`를 만드는 데 사용합니다. 생성자를 `PaginatedList<T>` 개체를 만들 수 없고, 생성자는 비동기 코드를 실행할 수 없습니다.

## <a name="add-paging-functionality-to-the-index-method"></a>인덱스 메서드에 페이징 기능 추가

*Students/Index.cshtml.cs*에서 `Student`의 형식을 `IList<Student>`에서 `PaginatedList<Student>`로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

다음 코드로 *Students/Index.cshtml.cs* `OnGetAsync`를 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

이전 코드는 페이지 인덱스, 현재 `sortOrder` 및 `currentFilter`를 메서드 서명에 추가합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

모든 매개 변수는 다음 경우에 null입니다.

* 페이지가 **학생** 링크에서 호출됩니다.
* 사용자가 페이징 또는 정렬 링크를 클릭하지 않았습니다.

페이징 링크를 클릭하면 페이지 인덱스 변수에 표시할 페이지 번호가 포함됩니다.

`CurrentSort`는 Razor 페이지를 현재 정렬 순서로 제공합니다. 현재 정렬 순서는 페이징하는 동안 정렬 순서를 유지하기 위해 페이징 링크에 포함되어야 합니다.

`CurrentFilter`는 Razor 페이지를 현재 필터 문자열로 제공합니다. `CurrentFilter` 값은:

* 페이징하는 동안 필터 설정을 유지하기 위해 페이징 링크에 포함되어야 합니다.
* 페이지를 다시 표시하는 경우 텍스트 상자에 복원되어야 합니다.

검색 문자열이 페이징하는 동안 변경되면 페이지가 1로 다시 설정됩니다. 새 필터로 인해 다른 데이터가 표시될 수 있으므로 페이지는 1로 재설정되어야 합니다. 검색 값을 입력하는 경우 및 **전송**을 선택한 경우:

* 검색 문자열이 변경됩니다.
* `searchString` 매개 변수가 null이 아닙니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync` 메서드가 학생 쿼리를 페이징을 지원하는 컬렉션 형식의 단일 학생 페이지로 변환합니다. 해당 단일 학생 페이지가 Razor 페이지에 전달됩니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

`PaginatedList.CreateAsync`에서 두 개의 물음표는 [Null 병합 연산자](/dotnet/csharp/language-reference/operators/null-conditional-operator)를 나타냅니다. Null 병합 연산자는 null 허용 형식에 대한 기본값을 정의합니다. 식 `(pageIndex ?? 1)`은 값이 있는 경우 `pageIndex`의 값을 반환함을 의미합니다. `pageIndex`에 값이 없으면 1을 반환합니다.

## <a name="add-paging-links-to-the-student-razor-page"></a>학생 Razor 페이지에 페이징 링크 추가

*Students/Index.cshtml*의 표시를 업데이트합니다. 변경 내용은 강조 표시되어 있습니다.

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

열 제목 링크는 쿼리 문자열을 사용하여 현재 검색 문자열을 `OnGetAsync` 메서드에 전달하므로 사용자가 필터 결과 내에서 정렬할 수 있습니다.

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

태그 도우미에 페이징 단추가 표시됩니다.

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

앱을 실행하고 학생 페이지로 이동합니다.

* 페이징이 작동하는지 확인하려면 다른 정렬 순서의 페이징 링크를 클릭합니다.
* 정렬 및 필터링을 통해 페이징이 제대로 작동하는지 확인하려면 검색 문자열을 입력하고 페이징을 다시 시도합니다.

![페이징 링크가 있는 학생 인덱스 페이지](sort-filter-page/_static/paging.png)

코드를 더 잘 이해하려면 다음을 수행합니다.

* *Students/Index.cshtml.cs*에서 `switch (sortOrder)`에 중단점을 설정합니다.
* `NameSort`, `DateSort`, `CurrentSort` 및 `Model.Student.PageIndex`에 대한 조사식을 추가합니다.
* *Students/Index.cshtml*에서 `@Html.DisplayNameFor(model => model.Student[0].LastName)`에 중단점을 설정합니다.

디버거를 단계별로 실행합니다.

## <a name="update-the-about-page-to-show-student-statistics"></a>학생 통계를 표시하도록 정보 페이지를 업데이트

이 단계에서는 각 등록 날짜에 대해 등록한 학생 수를 표시하도록 *Pages/About.cshtml*을 업데이트합니다. 업데이트는 그룹화를 사용하며 다음 단계를 포함합니다.

* **정보** 페이지에서 사용되는 데이터에 대한 보기 모델을 만듭니다.
* 정보 페이지를 업데이트하여 보기 모델을 사용합니다.

### <a name="create-the-view-model"></a>뷰 모델 만들기

*Models* 폴더에 *SchoolViewModels* 폴더를 만듭니다.

*SchoolViewModels* 폴더에서 다음 코드로 *EnrollmentDateGroup.cs*를 추가합니다.

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>페이지 모델 정보 업데이트

ASP.NET Core 2.2의 웹 템플릿에는 정보 페이지가 포함되지 않습니다. ASP.NET Core 2.2를 사용하는 경우 Razor 정보 페이지를 만드세요.

*Pages/About.cshtml.cs* 파일을 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

LINQ 문은 등록 날짜별로 학생 엔터티를 그룹화하고 각 그룹의 엔터티 수를 계산하며 결과를 `EnrollmentDateGroup` 뷰 모델 개체의 컬렉션에 저장합니다.

### <a name="modify-the-about-razor-page"></a>Razor 페이지 정보 수정

*Pages/About.cshtml* 파일의 코드를 다음 코드로 바꿉니다.

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

앱을 실행하고 정보 페이지로 이동합니다. 각 등록 날짜에 대한 학생 수가 테이블에 표시됩니다.

해결할 수 없는 문제가 발생한 경우 [이 단계에 완성된 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting)을 다운로드합니다.

![정보 페이지](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core 2.x 소스 디버깅](https://github.com/aspnet/AspNetCore.Docs/issues/4155)
* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=MDs7PFpoMqI)

다음 자습서에서는 앱은 마이그레이션을 사용하여 데이터 모델을 업데이트합니다.

::: moniker-end

> [!div class="step-by-step"]
> [이전](xref:data/ef-rp/crud)
> [다음](xref:data/ef-rp/migrations)
