::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="b61f5-101">*Data* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-101">Create a *Data* folder.</span></span>

<span data-ttu-id="b61f5-102">다음 `MvcMovieContext` 클래스를 *Data* 폴더에 추가:</span><span class="sxs-lookup"><span data-stu-id="b61f5-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="b61f5-103">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="b61f5-104">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="b61f5-105">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="b61f5-105">Add a database connection string</span></span>

<span data-ttu-id="b61f5-106">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="b61f5-107">NuGet 패키지 및 EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="b61f5-107">Add NuGet packages and EF tools</span></span>

<span data-ttu-id="b61f5-108">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-108">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="b61f5-109">위의 명령은 .NET CLI 및 여러 패키지의 Entity Framework Core 도구를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-109">The preceding commands add Entity Framework Core Tools for the .NET CLI and several packages to the project.</span></span> <span data-ttu-id="b61f5-110">스캐폴딩에는 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-110">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="b61f5-111">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="b61f5-111">Register the database context</span></span>

<span data-ttu-id="b61f5-112">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-112">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b61f5-113">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-113">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="b61f5-114">컴파일러 오류에 대한 검사로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-114">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b61f5-115">다음 `MvcMovieContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-115">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="b61f5-116">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-116">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="b61f5-117">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-117">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="b61f5-118">데이터베이스 연결 문자열 추가</span><span class="sxs-lookup"><span data-stu-id="b61f5-118">Add a database connection string</span></span>

<span data-ttu-id="b61f5-119">*appsettings.json* 파일에 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-119">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="b61f5-120">필요한 NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="b61f5-120">Add required NuGet packages</span></span>

<span data-ttu-id="b61f5-121">다음 .NET Core CLI 명령을 실행하여 SQLite 및 CodeGeneration.Design을 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-121">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="b61f5-122">스캐폴딩에는 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-122">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="b61f5-123">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="b61f5-123">Register the database context</span></span>

<span data-ttu-id="b61f5-124">*Startup.cs* 맨 위에 다음 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-124">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b61f5-125">데이터베이스 컨텍스트를 `Startup.ConfigureServices`의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-125">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="b61f5-126">오류에 대한 검사 방법으로 프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b61f5-126">Build the project as a check for errors.</span></span>
::: moniker-end