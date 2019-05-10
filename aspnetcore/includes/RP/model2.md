<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="49503-101">데이터베이스 컨텍스트 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="49503-101">Add a database context class</span></span>

<span data-ttu-id="49503-102">다음 `RazorPagesMovieContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-102">Add the following `RazorPagesMovieContext` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="49503-103">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="49503-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="49503-104">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="49503-105">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="49503-105">Add a database connection string</span></span>

<span data-ttu-id="49503-106">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="49503-107">필요한 NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="49503-107">Add required NuGet packages</span></span>

<span data-ttu-id="49503-108">다음 .NET Core CLI 명령을 실행하여 SQLite 및 CodeGeneration.Design을 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-108">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

<span data-ttu-id="49503-109">스캐폴딩에는 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-109">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="49503-110">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="49503-110">Register the database context</span></span>

<span data-ttu-id="49503-111">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="49503-112">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="49503-113">오류에 대한 검사 방법으로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="49503-113">Build the project as a check for errors.</span></span>
