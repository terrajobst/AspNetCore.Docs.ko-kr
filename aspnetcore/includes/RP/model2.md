<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="cd43d-101">데이터베이스 컨텍스트 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="cd43d-101">Add a database context class</span></span>

<span data-ttu-id="cd43d-102">RazorPagesMovie 프로젝트에서 *Data*라는 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="cd43d-103">다음 `RazorPagesMovieContext` 클래스를 *Data* 폴더에 추가:</span><span class="sxs-lookup"><span data-stu-id="cd43d-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cd43d-104">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="cd43d-105">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="cd43d-106">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="cd43d-106">Add a database connection string</span></span>

<span data-ttu-id="cd43d-107">다음 강조된 코드에 표시된 대로 연결 문자열을 *appsettings.json* 파일에 추가:</span><span class="sxs-lookup"><span data-stu-id="cd43d-107">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="cd43d-108">NuGet 패키지 및 EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="cd43d-108">Add NuGet packages and EF tools</span></span>

<span data-ttu-id="cd43d-109">RazorPagesMovie 프로젝트의 터미널을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-109">Open a terminal for the RazorPagesMovie project.</span></span>  <span data-ttu-id="cd43d-110">디자인/레이아웃 표시줄에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 터미널에서 **도구 > 열기**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-110">Right click the project name in the design/layout bar and go to **Tools > Open** in Terminal.</span></span> <span data-ttu-id="cd43d-111">터미널에서 다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-111">Run the following .NET Core CLI commands in the Termial:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="cd43d-112">위의 명령은 .NET CLI 및 여러 패키지의 Entity Framework Core 도구를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-112">The preceding commands add Entity Framework Core Tools for the .NET CLI and several packages to the project.</span></span> <span data-ttu-id="cd43d-113">스캐폴딩에는 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-113">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="cd43d-114">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="cd43d-114">Register the database context</span></span>

<span data-ttu-id="cd43d-115">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-115">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="cd43d-116">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-116">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="cd43d-117">필요한 NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="cd43d-117">Add required NuGet packages</span></span>

<span data-ttu-id="cd43d-118">다음 .NET Core CLI 명령을 실행하여 SQLite 및 CodeGeneration.Design을 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="cd43d-119">스캐폴딩에는 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="cd43d-120">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="cd43d-120">Register the database context</span></span>

<span data-ttu-id="cd43d-121">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="cd43d-122">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="cd43d-123">오류에 대한 검사 방법으로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="cd43d-123">Build the project as a check for errors.</span></span>
::: moniker-end
