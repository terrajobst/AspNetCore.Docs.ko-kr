---
title: ASP.NET Core에서 Entity Framework Core를 사용한 Razor 페이지 - 자습서 1/8
author: rick-anderson
description: Entity Framework Core를 사용하여 Razor 페이지 앱을 만드는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/22/2018
uid: data/ef-rp/intro
ms.openlocfilehash: dea178d169cb8f2ae807caa2ef495d4243b1f59f
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516898"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>ASP.NET Core에서 Entity Framework Core를 사용한 Razor 페이지 - 자습서 1/8

작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 샘플 웹앱은 EF(Entity Framework) Core를 사용하여 ASP.NET Core Razor Pages 앱을 만드는 방법을 보여줍니다.

샘플 앱은 가상 Contoso University의 웹 사이트입니다. 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다. 이 페이지는 Contoso University 샘플 앱을 빌드하는 방법을 설명하는 일련의 자습서 중 첫 번째 작업입니다.

[완성된 앱을 다운로드하거나 확인하세요.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [지침을 다운로드하세요](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>전제 조건

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

[Razor 페이지](xref:razor-pages/index)에 익숙함. 신규 프로그래머는 이 시리즈를 시작하기 전에 [Razor 페이지 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 완료해야 합니다.

## <a name="troubleshooting"></a>문제 해결

해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다. 도움을 얻으려면 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)에 질문을 게시하는 것이 좋습니다.

## <a name="the-contoso-university-web-app"></a>Contoso University 웹앱

이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.

사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다. 자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

이 사이트의 UI 스타일은 기본 제공 템플릿에서 생성된 것과 가깝습니다. 자습서는 UI가 아닌 Razor 페이지를 사용한 EF Core 자습서 위주로 설명됩니다.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>ContosoUniversity Razor Pages 웹앱 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.
* 새 ASP.NET Core 웹 애플리케이션을 만듭니다. 프로젝트 이름을 **ContosoUniversity**로 지정합니다. 코드를 복사/붙여넣을 때 네임스페이스와 일치할 수 있도록 프로젝트 이름을 *ContosoUniversity*로 지정하는 것이 중요합니다.
* 드롭다운에서 **ASP.NET Core 2.1**을 선택한 다음, **웹 애플리케이션**을 선택합니다.

이전 단계의 이미지는 [Razor 웹앱 만들기](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)를 참조하세요.
앱을 실행합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>사이트 스타일 설정

몇 가지 변경 내용으로 사이트 메뉴, 레이아웃 및 홈페이지를 설정합니다. *Pages/Shared/_Layout.cshtml*을 다음 변경 내용으로 업데이트합니다.

* 모든 “ContosoUniversity”를 “Contoso University”로 변경합니다. 세 번 나옵니다.

* **학생**, **강좌**, **강사** 및 **부서**에 대한 메뉴 항목을 추가하고 **연락처** 메뉴 항목을 삭제합니다.

변경 내용은 강조 표시되어 있습니다. (모든 표시가 표시되지는 *않습니다*.)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

*Pages/Index.cshtml*에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET 및 MVC에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>데이터 모델 만들기

Contoso University 앱에 대한 엔터티 클래스를 만듭니다. 다음과 같은 세 가지 엔터티로 시작합니다.

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

`Student` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다. `Course` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다. 학생은 여러 강좌에 등록할 수 있습니다. 강좌는 등록된 학생이 여러 명일 수 있습니다.

다음 섹션에서 각각에 대한 이러한 엔터티에 대한 클래스를 만듭니다.

### <a name="the-student-entity"></a>학생 엔터티

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

*모델* 폴더를 만듭니다. *모델* 폴더에서 다음 코드로 *Student.cs*라는 이름의 클래스 파일을 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID` 속성은 이 클래스에 해당하는 DB(데이터베이스) 테이블의 기본 키 열이 됩니다. 기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다. `classnameID`에서 `classname`은 클래스의 이름입니다. 기본 키가 자동으로 인식되는 경우 대안은 앞의 예제에서 `StudentID`입니다.

`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다. 탐색 속성은 이 엔터티와 관련된 다른 엔터티에 연결됩니다. 이 경우 `Student entity`의 `Enrollments` 속성은 해당 `Student`에 관련된 모든 `Enrollment` 엔터티를 포함합니다. 예를 들어 DB의 학생 행에 두 개의 관련 등록 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 `Enrollment` 엔터티를 포함합니다. 관련된 `Enrollment` 행은 `StudentID` 열에서 해당 학생의 기본 키 값을 포함하는 열입니다. 예를 들어 ID=1인 학생에 `Enrollment` 테이블의 두 개 행이 있다고 가정합니다. `Enrollment` 테이블에 `StudentID` = 1인 두 개의 행이 있습니다. `StudentID`는 `Student` 테이블에서 학생을 지정하는 `Enrollment` 테이블의 외래 키입니다.

탐색 속성이 여러 엔터티를 포함하는 경우 탐색 속성은 `ICollection<T>`와 같은 목록 유형이어야 합니다. `ICollection<T>`는 지정할 수 있으며, `List<T>` 또는 `HashSet<T>`와 같은 형식일 수 있습니다. `ICollection<T>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다. 여러 엔터티를 포함하는 탐색 속성은 다대다 및 일대다 관계에서 제공됩니다.

### <a name="the-enrollment-entity"></a>등록 엔터티

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

*모델* 폴더에서 다음 코드로 *Enrollment.cs*를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 `Student` 엔터티 같은 `ID` 대신 `classnameID` 패턴을 사용합니다. 일반적으로 개발자는 하나의 패턴을 선택하여 데이터 모델 전체에 사용합니다. 자습서의 뒷부분에서는 클래스 이름 없이 ID를 사용하여 더 손쉽게 데이터 모델에서 상속을 구현하는 내용이 나옵니다.

`Grade` 속성은 `enum`입니다. `Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 nullable이라는 것을 나타냅니다. Null인 등급은 0 등급과는 다릅니다. Null은 알려지지 않거나 아직 등록되지 않은 등급을 의미합니다.

`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다. `Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다. `Student` 엔터티는 여러 `Enrollment` 엔터티를 포함하는 `Student.Enrollments` 탐색 속성과 다릅니다.

`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다. `Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.

EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다. 예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 기본 키가 `ID`이므로 `StudentID`입니다. 외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다. 예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.

### <a name="the-course-entity"></a>강좌 엔터티

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

*모델* 폴더에서 다음 코드로 *Course.cs*를 만듭니다.

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 속성은 탐색 속성입니다. `Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.

앱은 `DatabaseGenerated` 특성을 통해 DB가 생성하도록 하는 대신 기본 키를 지정할 수 있습니다.

## <a name="scaffold-the-student-model"></a>학생 모델 스캐폴드

이 섹션에서는 학생 모델을 스캐폴드합니다. 즉, 스캐폴드 도구는 학생 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.

* 프로젝트를 빌드합니다.
* *Pages/Students* 폴더를 만듭니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 *Pages/Students* 폴더 > **추가** > **스캐폴드 항목 새로 만들기**를 마우스 오른쪽 단추로 클릭합니다.
* **스캐폴드 추가** 대화 상자에서 **Entity Framework(CRUD)를 사용한 Razor Pages** > **추가**를 선택합니다.

**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.

* **모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.
* **데이터 컨텍스트 클래스** 행에서 **+**(더하기)를 선택 하고 생성된 이름을 서명하고 **ContosoUniversity.Models.SchoolContext**로 변경합니다.
* **데이터 컨텍스트 클래스** 드롭다운에서 **ContosoUniversity.Models.SchoolContext**를 선택합니다.
* **추가**를 선택합니다.

![CRUD 대화 상자](intro/_static/s1.png)

이전 단계에서 문제가 발생한 경우 [영화 모델 스캐폴드](xref:tutorials/razor-pages/model#scaffold-the-movie-model)를 참조하세요.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

다음 명령을 실행하여 학생 모델을 스캐폴드합니다.

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

스캐폴드 프로세스는 다음 파일을 생성하고 변경했습니다.

### <a name="files-created"></a>생성된 파일

* *Pages/Students* 만들기, 삭제, 세부 정보, 편집, 인덱스입니다.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>파일 업데이트

* *Startup.cs* : 이 파일의 변경 내용은 다음 섹션에서 자세히 설명합니다.
* *appsettings.json* : 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열이 추가됩니다.

## <a name="examine-the-context-registered-with-dependency-injection"></a>종속성 주입을 사용하여 등록된 컨텍스트 검사

ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다. 서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다. 이러한 서비스(예: Razor 페이지)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다. DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.

스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.

*Startup.cs*의 `ConfigureServices` 메서드를 검사합니다. 강조 표시된 줄은 스캐폴더에서 추가되었습니다.

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다. 로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.

## <a name="update-main"></a>기본 업데이트

*Program.cs*에서 다음을 수행하는 `Main` 메서드를 수정합니다.

* 종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.
* [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)를 호출합니다.
* `EnsureCreated` 메서드가 완료되면 컨텍스트를 삭제합니다.

다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated`는 컨텍스트에 대한 데이터베이스가 있는지 확인합니다. 존재하는 경우 아무런 동작이 발생하지 않습니다. 존재하지 않는 경우 데이터베이스 및 해당하는 모든 스키마가 생성됩니다. `EnsureCreated`는 데이터베이스를 만드는 데 마이그레이션을 사용하지 않습니다. `EnsureCreated`를 사용하여 만든 데이터베이스는 나중에 마이그레이션을 사용하여 업데이트될 수 없습니다.

앱 시작 시 다음 작업 흐름을 허용하는 `EnsureCreated`가 호출됩니다.

* DB를 삭제합니다.
* DB 스키마를 변경합니다(예: `EmailAddress` 필드 추가).
* 앱을 실행합니다.
* `EnsureCreated`가 `EmailAddress` 열이 있는 DB를 만듭니다.

`EnsureCreated`는 스키마가 빠르게 발전하는 경우 개발 초기에 편리합니다. 나중에 자습서에서 DB가 삭제되고 마이그레이션이 사용됩니다.

### <a name="test-the-app"></a>앱 테스트

앱을 실행하고 쿠키 방침에 동의합니다. 이 앱은 개인 정보를 보관하지 않습니다. [EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)에서 쿠키 정책을 확인할 수 있습니다.

* **학생** 링크 및 **새로 만들기**를 차례로 선택합니다.
* 편집, 세부 정보 및 삭제 링크를 테스트합니다.

## <a name="examine-the-schoolcontext-db-context"></a>SchoolContext DB 컨텍스트 검사

특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 DB 컨텍스트 클래스입니다. 데이터 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다. 데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다. 이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.

*SchoolContext.cs*를 다음 코드로 업데이트합니다.

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다. EF Core 용어에서:

* 엔터티 집합은 일반적으로 DB 테이블에 해당합니다.
* 엔터티는 테이블의 행에 해당합니다.

`DbSet<Enrollment>` 및 `DbSet<Course>`를 생략할 수 있습니다. `Student` 엔터티는 `Enrollment` 엔터티를 참조하고 `Enrollment` 엔터티는 `Course` 엔터티를 참조하기 때문에 EF Core는 이를 암시적으로 포함합니다. 이 자습서의 경우 `DbSet<Enrollment>` 및 `DbSet<Course>`를 `SchoolContext`에 유지합니다.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다. LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다. LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다. 기본적으로 LocalDB는 *.mdf* DB 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>코드를 추가하여 테스트 데이터로 DB 초기화

EF Core가 빈 DB를 만듭니다. 이 섹션에서는 테스트 데이터로 채울 `Initialize` 메서드가 작성됩니다.

*Data* 폴더에서 *DbInitializer.cs*라는 새 클래스 파일을 만들고 다음 코드를 추가합니다.

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

참고: 앞의 코드는 `Data`가 아닌 `Models`을 네임스페이스(`namespace ContosoUniversity.Models`)에 사용합니다. `Models`는 스캐폴더에서 생성된 코드와 일치합니다. 자세한 내용은 [이 GitHub 스캐폴딩 문제](https://github.com/aspnet/Scaffolding/issues/822)를 참조하세요.

코드는 DB에 학생이 있는지를 확인합니다. DB에 학생이 없는 경우 DB는 테스트 데이터로 초기화됩니다. `List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.

`EnsureCreated` 메서드는 자동으로 DB 컨텍스트에 대한 DB를 만듭니다. DB가 있는 경우 `EnsureCreated`는 DB를 수정하지 않고 반환합니다.

*Program.cs*에서 `Initialize`를 호출하도록 `Main` 메서드를 수정합니다.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

학생 레코드를 삭제하고 앱을 다시 시작합니다. DB를 초기화하지 않으면 `Initialize`에서 중단점을 설정하여 문제를 진단합니다.

## <a name="view-the-db"></a>DB 보기

데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다. 따라서 데이터베이스 이름은 “SchoolContext-{GUID}”입니다. GUID는 사용자에 따라 다릅니다.
Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.
SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 클릭합니다.

**테이블** 노드를 확장합니다.

**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기**를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.

## <a name="asynchronous-code"></a>비동기 코드

비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.

웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다. 이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다. 동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다. 비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다. 따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.

비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다. 트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.

다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async` 키워드는 컴파일러가 다음을 수행하도록 합니다.
  * 메서드 본문의 부분에 대한 콜백을 생성합니다.
  * 반환되는 [작업](/dotnet/api/system.threading.tasks.task) 개체를 자동으로 만듭니다. 자세한 내용은 [작업 반환 형식](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)을 참조하세요.

* 암시적 반환 형식 `Task`는 진행 중인 작업을 나타냅니다.
* `await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다. 첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다. 두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.
* `ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.

EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.

* 쿼리 또는 명령을 DB에 보내는 명령문만 비동기적으로 실행됩니다. 여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다. `var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.
* EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.
* 비동기 코드의 성능 이점을 활용하려면 쿼리를 DB에 보내는 EF Core 메서드를 호출하는 경우 라이브러리 패키지(예: 페이징)가 비동기를 사용하는지 확인합니다.

.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.

다음 자습서에서는 기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 검사합니다.



## <a name="additional-resources"></a>추가 자료

* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [다음](xref:data/ef-rp/crud)
