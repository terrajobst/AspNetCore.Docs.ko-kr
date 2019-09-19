---
title: ASP.NET Core MVC 앱에 모델 추가
author: rick-anderson
description: 간단한 ASP.NET Core 앱에 모델을 추가합니다.
ms.author: riande
ms.date: 8/15/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: b0efaf76cb2172f5b7568e42065b99b1259949de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082004"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="28879-103">ASP.NET Core MVC 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="28879-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="28879-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="28879-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="28879-105">이 섹션에서는 데이터베이스에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="28879-106">이러한 클래스는 **M**VC 앱의 "**M**odel" 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="28879-107">이러한 클래스를 EF Core([Entity Framework Core](/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="28879-108">EF Core는 작성해야 하는 데이터 액세스 코드를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="28879-109">직접 만드는 모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(**P**lain **O**ld **C**LR **O**bject) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="28879-110">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="28879-111">이 자습서에서는 먼저 모델 클래스를 작성하면 EF Core가 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28879-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="28879-112">이 문서에서 설명하지 않는 대체 방법은 기존 데이터베이스에서 모델 클래스를 생성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="28879-113">이 방법에 대한 정보는 [ASP.NET Core - 기존 데이터베이스](/ef/core/get-started/aspnetcore/existing-db)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28879-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="28879-114">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="28879-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28879-116">*Models* 폴더> **추가** > **클래스**를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="28879-117">파일 이름을 *Movie.cs*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-118">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="28879-119">*Movie.cs* 파일을 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

---

<span data-ttu-id="28879-120">다음 코드로 *Movie.cs* 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-120">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="28879-121">`Movie` 클래스에는 기본 키에 대해 데이터베이스에 필요한 `Id` 필드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-121">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="28879-122">`ReleaseDate`에 대한 [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 특성은 데이터 형식(`Date`)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-122">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="28879-123">이 특성 사용:</span><span class="sxs-lookup"><span data-stu-id="28879-123">With this attribute:</span></span>

  * <span data-ttu-id="28879-124">사용자는 날짜 필드에 시간 정보를 입력할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-124">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="28879-125">시간 정보가 아니라 날짜만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-125">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="28879-126">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)는 이후 자습서에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="28879-126">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="28879-127">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="28879-127">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-128">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28879-129">**도구** 메뉴에서 **NuGet 패키지 관리자** > PMC(**패키지 관리자 콘솔**)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-129">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="28879-131">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-131">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer -IncludePrerelease
```

<span data-ttu-id="28879-132">앞의 명령은 EF Core SQL Server 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-132">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="28879-133">이 공급자 패키지는 EF Core 패키지를 종속성으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-133">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="28879-134">추가 패키지는 자습서 뒷부분의 스캐폴딩 단계에서 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-134">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-135">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-135">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="28879-136">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-136">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="28879-137">앞의 명령은 다음을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-137">The preceding commands add:</span></span>

* <span data-ttu-id="28879-138">.NET CLI에 대한 Entity Framework Core 도구.</span><span class="sxs-lookup"><span data-stu-id="28879-138">The Entity Framework Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="28879-139">EF Core 패키지를 종속성으로 설치하는 EF Core SQLite 공급자.</span><span class="sxs-lookup"><span data-stu-id="28879-139">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="28879-140">스캐폴딩에 필요한 패키지: `Microsoft.VisualStudio.Web.CodeGeneration.Design` 및 `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="28879-140">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="28879-141">데이터베이스 컨텍스트 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="28879-141">Create a database context class</span></span>

<span data-ttu-id="28879-142">`Movie` 모델에 대한 EF Core 기능(만들기, 읽기, 업데이트, 삭제)을 조정하려면 데이터베이스 컨텍스트 클래스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-142">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="28879-143">데이터베이스 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생되며 데이터 모델에 포함할 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-143">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="28879-144">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28879-144">Create a *Data* folder.</span></span>

<span data-ttu-id="28879-145">다음 코드로 *Data/MvcMovieContext.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-145">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="28879-146">이전 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28879-146">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="28879-147">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-147">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="28879-148">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-148">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="28879-149">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="28879-149">Register the database context</span></span>

<span data-ttu-id="28879-150">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-150">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="28879-151">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-151">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="28879-152">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-152">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="28879-153">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="28879-153">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="28879-154">이 섹션에서는 DI 컨테이너에 데이터베이스 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-154">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="28879-155">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-155">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="28879-156">`Startup.ConfigureServices`에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-156">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-157">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="28879-159">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-159">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="28879-160">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-160">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="28879-161">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="28879-161">Add a database connection string</span></span>

<span data-ttu-id="28879-162">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-162">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-164">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="28879-165">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-165">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="28879-166">동영상 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="28879-166">Scaffold movie pages</span></span>

<span data-ttu-id="28879-167">스캐폴딩 도구를 사용하여 동영상 모델에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-167">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-168">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28879-169">**솔루션 탐색기**에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-169">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위의 단계 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="28879-171">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-171">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="28879-173">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-173">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="28879-174">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="28879-174">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="28879-175">**데이터 컨텍스트 클래스:** *MvcMovieContext(MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="28879-175">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc3.png)

* <span data-ttu-id="28879-177">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="28879-177">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="28879-178">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="28879-178">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="28879-179">**추가** 선택</span><span class="sxs-lookup"><span data-stu-id="28879-179">Select **Add**</span></span>

<span data-ttu-id="28879-180">Visual Studio에서 다음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28879-180">Visual Studio creates:</span></span>

* <span data-ttu-id="28879-181">동영상 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="28879-181">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="28879-182">만들기, 삭제, 세부 정보, 편집 및 인덱스 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="28879-182">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="28879-183">이 파일의 자동 생성을 *스캐폴딩*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-183">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="28879-184">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28879-184">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="28879-185">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="28879-185">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="28879-186">Linux에서 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="28879-186">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="28879-187">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-187">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="28879-188">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-188">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="28879-189">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="28879-189">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="28879-190">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-190">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="28879-191">데이터베이스가 없으므로 아직 스캐폴드된 페이지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-191">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="28879-192">앱을 실행하고 **동영상 앱** 링크를 클릭하면 ‘데이터베이스를 열 수 없음’  또는 *‘해당 테이블이 없음:* Movie’ 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-192">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="28879-193">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="28879-193">Initial migration</span></span>

<span data-ttu-id="28879-194">EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28879-194">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="28879-195">마이그레이션은 데이터 모델과 일치하도록 데이터베이스를 만들고 업데이트할 수 있는 도구 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-195">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-196">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28879-197">**도구** 메뉴에서 **NuGet 패키지 관리자** > PMC(**패키지 관리자 콘솔**)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-197">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="28879-198">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-198">In the PMC, enter the following commands:</span></span>

```console
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="28879-199">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-199">`Add-Migration InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="28879-200">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-200">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="28879-201">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-201">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="28879-202">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-202">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="28879-203">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-203">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="28879-204">`Update-Database`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-204">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="28879-205">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-205">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="28879-206">데이터베이스 업데이트 명령은 다음 경고를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-206">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="28879-207">엔터티 형식 ‘Movie’에서 10진수 열 ‘Price’의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-207">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="28879-208">그러면 값이 기본 전체 자릿수 및 소수 자릿수에 적합하지 않은 경우 자동으로 잘립니다.</span><span class="sxs-lookup"><span data-stu-id="28879-208">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="28879-209">‘HasColumnType()’를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-209">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="28879-210">해당 경고를 무시할 수 있지만 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-210">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-211">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-211">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="28879-212">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-212">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="28879-213">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-213">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="28879-214">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-214">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="28879-215">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-215">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="28879-216">이는 첫 번째 마이그레이션이므로 생성된 클래스에는 데이터베이스 스키마를 만드는 코드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-216">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="28879-217">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일에서 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-217">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="28879-218">`ef database update`: 이전 명령이 만든 최신 마이그레이션으로 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-218">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="28879-219">이 명령은 데이터베이스를 만드는 `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-219">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [ more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="28879-220">InitialCreate 클래스</span><span class="sxs-lookup"><span data-stu-id="28879-220">The InitialCreate class</span></span>

<span data-ttu-id="28879-221">*Migrations/{timestamp}_InitialCreate.cs* 마이그레이션 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-221">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

 <span data-ttu-id="28879-222">`Up` 메서드는 Movie 테이블을 만들고 `Id`를 기본 키로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-222">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="28879-223">`Down` 메서드는 `Up` 마이그레이션에 의해 변경된 스키마를 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="28879-223">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="28879-224">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="28879-224">Test the app</span></span>

* <span data-ttu-id="28879-225">앱을 실행하고 **동영상 앱** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-225">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="28879-226">다음 중 하나와 비슷한 예외가 발생하는 경우:</span><span class="sxs-lookup"><span data-stu-id="28879-226">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-227">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="28879-229">[마이그레이션 단계](#migration)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-229">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="28879-230">**만들기** 페이지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-230">Test the **Create** page.</span></span> <span data-ttu-id="28879-231">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-231">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="28879-232">`Price` 필드에는 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-232">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="28879-233">소수점으로 쉼표(",")를 사용하는 영어가 아닌 로캘 및 미국 영어가 아닌 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 글로벌화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-233">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="28879-234">세계화 지침은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28879-234">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="28879-235">**편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-235">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="28879-236">컨트롤러의 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="28879-236">Dependency injection in the controller</span></span>

<span data-ttu-id="28879-237">*Controllers/MoviesController.cs* 파일을 열고 생성자를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-237">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="28879-238">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-238">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="28879-239">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-239">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="28879-240">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="28879-240">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="28879-241">이 자습서의 앞부분에서 언급했듯이 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 뷰에 전달할 수 있는 방법이 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-241">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="28879-242">`ViewData` 사전은 확인할 정보를 전달하는 편리한 런타임에 바인딩된 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-242">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="28879-243">또한 MVC는 확인할 강력한 형식의 모델 개체를 전달하는 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-243">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="28879-244">이 강력한 형식의 방법을 통해 컴파일 시간 코드 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-244">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="28879-245">스캐폴딩 메커니즘은 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-245">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="28879-246">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-246">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="28879-247">`id` 매개 변수는 일반적으로 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-247">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="28879-248">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-248">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="28879-249">`movies` 컨트롤러에 대한 컨트롤러(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="28879-249">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="28879-250">`details`에 대한 작업(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="28879-250">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="28879-251">1에 대한 ID(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="28879-251">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="28879-252">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-252">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="28879-253">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-253">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="28879-254">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-254">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="28879-255">영화가 있으면 `Movie` 모델의 인스턴스를 `Details` 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-255">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="28879-256">*Views/Movies/Details.cshtml* 파일의 내용을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-256">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="28879-257">뷰 파일 맨 위에 있는 `@model` 문은 뷰에 필요한 개체의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-257">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="28879-258">동영상 컨트롤러가 만들어진 후 다음 `@model` 문이 포함되었습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-258">When the movie controller was created, the following `@model` statement was included:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="28879-259">이 `@model` 지시문을 사용하면 컨트롤러가 뷰에 전달한 동영상에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-259">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="28879-260">`Model` 개체는 강력한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-260">The `Model` object is strongly typed.</span></span> <span data-ttu-id="28879-261">예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-261">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="28879-262">또한 `Create` 및 `Edit` 메서드 및 뷰는 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-262">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="28879-263">영화 컨트롤러에서 *Index.cshtml* 뷰 및 `Index` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-263">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="28879-264">코드가 `View` 메서드를 호출하는 경우 `List` 개체를 생성하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-264">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="28879-265">이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-265">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="28879-266">동영상 컨트롤러가 만들어진 경우 스캐폴딩은 다음 `@model` 문을 *Index.cshtml* 파일의 맨 위에 포함했습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-266">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="28879-267">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-267">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="28879-268">예를 들어 *Index.cshtml* 뷰에서 코드는 강력한 형식의 `Model` 개체에 `foreach` 문을 포함한 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-268">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="28879-269">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie`으로 입력됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-269">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="28879-270">즉, 여러 가지 이점 중에서 코드의 컴파일 시간 검사를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-270">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28879-271">추가 자료</span><span class="sxs-lookup"><span data-stu-id="28879-271">Additional resources</span></span>

* [<span data-ttu-id="28879-272">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="28879-272">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="28879-273">전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="28879-273">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="28879-274">[이전 뷰 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="28879-274">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="28879-275">데이터 모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="28879-275">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-276">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-276">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28879-277">*Models* 폴더> **추가** > **클래스**를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-277">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="28879-278">클래스의 이름을 **동영상**으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-278">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-279">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-279">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="28879-280">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-280">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="28879-281">동영상 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="28879-281">Scaffold the movie model</span></span>

<span data-ttu-id="28879-282">이 섹션에서는 동영상 모델을 스캐폴 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-282">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="28879-283">즉, 스캐폴드 도구는 동영상 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-283">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-284">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28879-285">**솔루션 탐색기**에서 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **> 추가 > 스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-285">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![위의 단계 보기](adding-model/_static/add_controller21.png)

<span data-ttu-id="28879-287">**스캐폴드 추가** 대화 상자에서 **보기 포함 MVC 컨트롤러, Entity Framework > 추가 사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-287">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![스캐폴드 추가 대화 상자](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="28879-289">**컨트롤러 추가** 대화 상자를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-289">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="28879-290">**모델 클래스:** *Movie(MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="28879-290">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="28879-291">**데이터 컨텍스트 클래스:** **+** 아이콘을 선택하고 기본값 **MvcMovie.Models.MvcMovieContext**를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-291">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![데이터 컨텍스트 추가](adding-model/_static/dc.png)

* <span data-ttu-id="28879-293">**보기:** 확인된 각 옵션의 기본값 선택 유지</span><span class="sxs-lookup"><span data-stu-id="28879-293">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="28879-294">**컨트롤러 이름:** 기본값 *MoviesController* 유지</span><span class="sxs-lookup"><span data-stu-id="28879-294">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="28879-295">**추가** 선택</span><span class="sxs-lookup"><span data-stu-id="28879-295">Select **Add**</span></span>

![컨트롤러 추가 대화 상자](adding-model/_static/add_controller2.png)

<span data-ttu-id="28879-297">Visual Studio에서 다음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28879-297">Visual Studio creates:</span></span>

* <span data-ttu-id="28879-298">Entity Framework Core [데이터베이스 컨텍스트 클래스](xref:data/ef-mvc/intro#create-the-database-context)(*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="28879-298">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="28879-299">동영상 컨트롤러(*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="28879-299">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="28879-300">만들기, 삭제, 세부 정보, 편집 및 인덱스 페이지에 대한 Razor 뷰 파일(*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="28879-300">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="28879-301">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)(만들기, 읽기, 업데이트 및 삭제) 작업 메서드와 뷰 및 데이터베이스 컨텍스트의 자동 생성을 *스캐폴딩*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-301">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="28879-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28879-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="28879-303">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="28879-303">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="28879-304">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-304">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="28879-305">Linux에서 스캐폴드 도구 경로를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="28879-305">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="28879-306">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-306">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="28879-307">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-307">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="28879-308">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="28879-308">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="28879-309">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-309">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="28879-310">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-310">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="28879-311">응용 프로그램을 실행하고 **Mvc Movie** 링크를 클릭하면 다음과 유사한 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-311">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-312">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-312">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-313">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-313">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="28879-314">데이터베이스를 만들어야 하며 이를 수행하기 위해 EF Core [마이그레이션](xref:data/ef-mvc/migrations) 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-314">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="28879-315">마이그레이션을 통해 데이터 모델과 일치하는 데이터베이스를 만들고 데이터 모델 변경 시 데이터베이스 스키마를 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-315">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="28879-316">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="28879-316">Initial migration</span></span>

<span data-ttu-id="28879-317">이 섹션에서는 다음 작업이 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-317">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="28879-318">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-318">Add an initial migration.</span></span>
* <span data-ttu-id="28879-319">초기 마이그레이션을 사용하여 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-319">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-320">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="28879-321">**도구** 메뉴에서 **NuGet 패키지 관리자** > PMC(**패키지 관리자 콘솔**)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-321">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 메뉴](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="28879-323">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-323">In the PMC, enter the following commands:</span></span>

   ```console
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="28879-324">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-324">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="28879-325">데이터베이스 스키마는 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-325">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="28879-326">`Initial` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-326">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="28879-327">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-327">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="28879-328">자세한 내용은 <xref:data/ef-mvc/migrations>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28879-328">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="28879-329">`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/{time-stamp}_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-329">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-330">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-330">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="28879-331">`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-331">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="28879-332">데이터베이스 스키마는 *Data/MvcMovieContext.cs* 파일에서 `MvcMovieContext` 클래스에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-332">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="28879-333">`InitialCreate` 인수는 마이그레이션 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-333">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="28879-334">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-334">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="28879-335">종속성 주입을 사용하여 등록된 컨텍스트 검사</span><span class="sxs-lookup"><span data-stu-id="28879-335">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="28879-336">ASP.NET Core는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="28879-337">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-337">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="28879-338">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="28879-339">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="28879-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28879-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28879-340">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28879-341">스캐폴딩 도구는 자동으로 DB 컨텍스트를 만들고 DI 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-341">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="28879-342">다음 `Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-342">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="28879-343">강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-343">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="28879-344">`MvcMovieContext`는 `Movie` 모델에 대한 EF Core 기능(만들기, 읽기, 업데이트, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-344">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="28879-345">데이터 컨텍스트(`MvcMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-345">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="28879-346">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-346">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="28879-347">이전 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="28879-347">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="28879-348">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-348">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="28879-349">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-349">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="28879-350">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-350">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="28879-351">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-351">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="28879-352">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28879-352">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="28879-353">DB 컨텍스트를 만들고 DI 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-353">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="28879-354">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="28879-354">Test the app</span></span>

* <span data-ttu-id="28879-355">앱을 실행하고 브라우저에서 `/Movies`를 URL에 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="28879-355">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="28879-356">다음과 비슷한 데이터베이스 예외가 발생하는 경우:</span><span class="sxs-lookup"><span data-stu-id="28879-356">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="28879-357">[마이그레이션 단계](#pmc)를 누락했습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-357">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="28879-358">**만들기** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-358">Test the **Create** link.</span></span> <span data-ttu-id="28879-359">데이터를 입력하고 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-359">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="28879-360">`Price` 필드에는 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-360">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="28879-361">소수점으로 쉼표(",")를 사용하는 영어가 아닌 로캘 및 미국 영어가 아닌 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 글로벌화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-361">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="28879-362">세계화 지침은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="28879-362">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="28879-363">**편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-363">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="28879-364">`Startup` 클래스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-364">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="28879-365">앞에서 강조 표시된 코드는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가된 영화 데이터베이스 컨텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-365">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="28879-366">`services.AddDbContext<MvcMovieContext>(options =>`는 사용할 데이터베이스 및 연결 문자열을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-366">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="28879-367">`=>`은 [람다 연산자](/dotnet/articles/csharp/language-reference/operators/lambda-operator)입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-367">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="28879-368">*Controllers/MoviesController.cs* 파일을 열고 생성자를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-368">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="28879-369">생성자는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 컨트롤러에 데이터베이스 컨텍스트(`MvcMovieContext`)를 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-369">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="28879-370">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-370">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="28879-371">강력한 형식의 모델 및 @model 키워드</span><span class="sxs-lookup"><span data-stu-id="28879-371">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="28879-372">이 자습서의 앞부분에서 언급했듯이 컨트롤러가 `ViewData` 사전을 사용하여 데이터 또는 개체를 뷰에 전달할 수 있는 방법이 표시되었습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-372">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="28879-373">`ViewData` 사전은 확인할 정보를 전달하는 편리한 런타임에 바인딩된 방법을 제공하는 동적 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="28879-373">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="28879-374">또한 MVC는 확인할 강력한 형식의 모델 개체를 전달하는 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-374">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="28879-375">이렇게 강력하게 형식화된 방법을 사용하면 코드를 검사하는 컴파일 시간을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-375">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="28879-376">스캐폴딩 메커니즘은 메서드 및 뷰를 만든 경우 `MoviesController` 클래스 및 뷰에서 이 방법(즉, 강력한 형식의 모델 전달)을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-376">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="28879-377">*Controllers/MoviesController.cs* 파일에서 생성된 `Details` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-377">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="28879-378">`id` 매개 변수는 일반적으로 경로 데이터 변수로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-378">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="28879-379">예를 들어 `https://localhost:5001/movies/details/1`은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-379">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="28879-380">`movies` 컨트롤러에 대한 컨트롤러(첫 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="28879-380">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="28879-381">`details`에 대한 작업(두 번째 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="28879-381">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="28879-382">1에 대한 ID(마지막 URL 세그먼트)</span><span class="sxs-lookup"><span data-stu-id="28879-382">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="28879-383">다음과 같이 쿼리 문자열을 사용하여 `id`에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-383">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="28879-384">ID 값이 제공되지 않는 경우에 `id` 매개 변수가 [nullable 형식](/dotnet/csharp/programming-guide/nullable-types/index)(`int?`)으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-384">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="28879-385">[람다 식](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)을 `FirstOrDefaultAsync`에 전달하여 경로 데이터 또는 쿼리 문자열 값과 일치하는 영화 엔터티를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-385">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="28879-386">영화가 있으면 `Movie` 모델의 인스턴스를 `Details` 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-386">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="28879-387">*Views/Movies/Details.cshtml* 파일의 내용을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-387">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="28879-388">뷰 파일 맨 위에 있는 `@model` 문을 포함하여 뷰에서 필요로 하는 개체의 유형을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-388">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="28879-389">영화 컨트롤러를 만들 때 *Details.cshtml* 파일의 맨 위에 다음 `@model` 문이 자동으로 포함되었습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-389">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="28879-390">이 `@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-390">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="28879-391">예를 들어 *Details.cshtml* 뷰에서 코드는 각 영화 필드를 강력한 형식의 `Model` 개체를 사용하여 `DisplayNameFor` 및 `DisplayFor` HTML 도우미에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-391">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="28879-392">또한 `Create` 및 `Edit` 메서드 및 뷰는 `Movie` 모델 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-392">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="28879-393">영화 컨트롤러에서 *Index.cshtml* 뷰 및 `Index` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-393">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="28879-394">코드가 `View` 메서드를 호출하는 경우 `List` 개체를 생성하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-394">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="28879-395">이 코드는 `Index` 작업 메서드의 `Movies` 목록을 뷰에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-395">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="28879-396">영화 컨트롤러를 만들 때 스캐폴딩에서는 *Index.cshtml* 파일의 맨 위에 다음 `@model` 문을 자동으로 포함했습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-396">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="28879-397">`@model` 지시문을 사용하면 강력한 형식인 `Model` 개체를 사용하여 컨트롤러가 뷰에 전달된 영화 목록에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-397">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="28879-398">예를 들어 *Index.cshtml* 뷰에서 코드는 강력한 형식의 `Model` 개체에 `foreach` 문을 포함한 영화를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="28879-398">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="28879-399">`Model` 개체가 강력한 형식이기 때문에(`IEnumerable<Movie>` 개체처럼) 루프의 각 항목은 `Movie`으로 입력됩니다.</span><span class="sxs-lookup"><span data-stu-id="28879-399">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="28879-400">즉, 여러 가지 이점 중에서 코드를 검사하는 컴파일 시간을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="28879-400">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28879-401">추가 자료</span><span class="sxs-lookup"><span data-stu-id="28879-401">Additional resources</span></span>

* [<span data-ttu-id="28879-402">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="28879-402">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="28879-403">전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="28879-403">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="28879-404">[이전 뷰 추가](adding-view.md)
> [다음 SQL 사용](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="28879-404">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end
