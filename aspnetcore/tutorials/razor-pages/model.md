---
title: ASP.NET Core에서 Razor 페이지 앱에 모델 추가
author: rick-anderson
description: Entity Framework Core(EF Core)를 사용하여 데이터베이스에서 영화를 관리하기 위한 클래스를 추가하는 방법을 알아봅니다.
ms.author: riande
ms.date: 11/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f2c9c2fc8112ef8a1a5afdbe448de6319c43521d
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761229"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="98243-103">ASP.NET Core에서 Razor 페이지 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="98243-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="98243-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="98243-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="98243-105">이 섹션에서는 플랫폼 간 [SQLite 데이터베이스](https://www.sqlite.org/index.html)에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="98243-106">ASP.NET Core 템플릿으로 만든 앱이 SQLite 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="98243-107">앱의 모델 클래스를 [EF Core(Entity Framework Core)](/ef/core)([SQLite EF Core 데이터베이스 공급자](/ef/core/providers/sqlite))와 함께 사용하여 데이터베이스를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="98243-108">EF Core는 데이터 액세스를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="98243-109">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="98243-110">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="98243-111">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="98243-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98243-113">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="98243-114">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-114">Name the folder *Models*.</span></span>

<span data-ttu-id="98243-115">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-115">Right click the *Models* folder.</span></span> <span data-ttu-id="98243-116">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="98243-117">클래스의 이름을 **동영상**으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="98243-119">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="98243-120">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-121">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="98243-122">솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="98243-123">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="98243-124">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="98243-125">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="98243-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="98243-126">왼쪽 창에서 **일반**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="98243-127">가운데 창에서 **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="98243-128">클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="98243-129">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="98243-130">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="98243-130">Scaffold the movie model</span></span>

<span data-ttu-id="98243-131">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="98243-132">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98243-134">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="98243-135">*페이지* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="98243-136">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-136">Name the folder *Movies*</span></span>

<span data-ttu-id="98243-137">*페이지/동영상* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="98243-139">**스캐폴드 추가** 대화 상자에서 **Entity Framework(CRUD)를 사용한 Razor Pages** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="98243-141">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="98243-142">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="98243-143">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름을 RazorPagesMovie.**Models**.RazorPagesMovieContext에서 RazorPagesMovie.**Data**.RazorPagesMovieContext로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="98243-144">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="98243-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="98243-145">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="98243-146">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-146">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/3/arp.png)

<span data-ttu-id="98243-148">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="98243-150">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="98243-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="98243-151">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="98243-152">**Windows의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="98243-153">**macOS 및 Linux의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-154">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="98243-155">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="98243-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="98243-156">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="98243-157">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="98243-158">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="98243-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98243-160">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="98243-161">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="98243-162">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="98243-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="98243-163">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="98243-163">Updated</span></span>

* <span data-ttu-id="98243-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="98243-164">*Startup.cs*</span></span>

<span data-ttu-id="98243-165">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="98243-166">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="98243-167">스캐폴드 프로세스는 다음 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="98243-168">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="98243-169">생성된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="98243-170">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="98243-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98243-172">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="98243-173">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-173">Add an initial migration.</span></span>
* <span data-ttu-id="98243-174">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="98243-175">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="98243-177">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-179">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="98243-180">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="98243-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="98243-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="98243-182">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="98243-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="98243-183">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="98243-184">migrations 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="98243-185">이 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="98243-186">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="98243-187">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="98243-188">`update` 명령은 적용되지 않은 마이그레이션에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="98243-189">이 경우 `update`는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="98243-191">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="98243-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="98243-192">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="98243-193">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="98243-194">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="98243-195">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="98243-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="98243-196">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="98243-197">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="98243-198">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="98243-199">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="98243-200">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="98243-201">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="98243-202">이전 코드에서는 엔터티 집합에 대한 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-202">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="98243-203">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="98243-204">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="98243-205">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="98243-206">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="98243-208">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-209">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="98243-210">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="98243-211">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-211">Test the app</span></span>

* <span data-ttu-id="98243-212">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="98243-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="98243-213">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="98243-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="98243-214">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="98243-215">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-215">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="98243-217">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="98243-218">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="98243-219">세계화 지침은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="98243-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="98243-220">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="98243-221">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98243-222">추가 자료</span><span class="sxs-lookup"><span data-stu-id="98243-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="98243-223">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="98243-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="98243-224">이 섹션에서는 플랫폼 간 [SQLite 데이터베이스](https://www.sqlite.org/index.html)에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="98243-225">ASP.NET Core 템플릿으로 만든 앱이 SQLite 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="98243-226">앱의 모델 클래스를 [EF Core(Entity Framework Core)](/ef/core)([SQLite EF Core 데이터베이스 공급자](/ef/core/providers/sqlite))와 함께 사용하여 데이터베이스를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="98243-227">EF Core는 데이터 액세스를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="98243-228">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="98243-229">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="98243-230">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="98243-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98243-232">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="98243-233">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-233">Name the folder *Models*.</span></span>

<span data-ttu-id="98243-234">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-234">Right click the *Models* folder.</span></span> <span data-ttu-id="98243-235">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="98243-236">클래스의 이름을 **동영상**으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="98243-238">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="98243-239">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-240">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="98243-241">솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="98243-242">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="98243-243">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="98243-244">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="98243-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="98243-245">왼쪽 창에서 **일반**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="98243-246">가운데 창에서 **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="98243-247">클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="98243-248">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="98243-249">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="98243-249">Scaffold the movie model</span></span>

<span data-ttu-id="98243-250">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="98243-251">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98243-253">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="98243-254">*페이지* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="98243-255">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-255">Name the folder *Movies*</span></span>

<span data-ttu-id="98243-256">*페이지/동영상* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="98243-258">**스캐폴드 추가** 대화 상자에서 **Entity Framework(CRUD)를 사용한 Razor Pages** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="98243-260">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="98243-261">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="98243-262">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름인 **RazorPagesMovie.Models.RazorPagesMovieContext**를 수용합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="98243-263">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-263">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arp.png)

<span data-ttu-id="98243-265">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="98243-267">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="98243-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="98243-268">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-268">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="98243-269">**Windows의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-269">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="98243-270">**macOS 및 Linux의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-270">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-271">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-271">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="98243-272">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="98243-272">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="98243-273">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-273">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="98243-274">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-274">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="98243-275">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-275">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="98243-276">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="98243-276">Files created</span></span>

* <span data-ttu-id="98243-277">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-277">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="98243-278">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="98243-278">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="98243-279">파일 업데이트됨</span><span class="sxs-lookup"><span data-stu-id="98243-279">File updated</span></span>

* <span data-ttu-id="98243-280">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="98243-280">*Startup.cs*</span></span>

<span data-ttu-id="98243-281">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-281">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="98243-282">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="98243-282">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98243-284">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-284">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="98243-285">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-285">Add an initial migration.</span></span>
* <span data-ttu-id="98243-286">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-286">Update the database with the initial migration.</span></span>

<span data-ttu-id="98243-287">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-287">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="98243-289">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-289">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="98243-290">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-290">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="98243-291">스키마는 `DbContext`에 지정된 모델을 기반으로 합니다(*RazorPagesMovieContext.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="98243-291">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="98243-292">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-292">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="98243-293">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-293">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="98243-294">자세한 내용은 <xref:data/ef-mvc/migrations>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="98243-294">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="98243-295">`Update-Database` 명령은 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-295">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="98243-296">`Up` 메서드는 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-296">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-298">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="98243-299">이전 명령은 다음 경고를 생성합니다. "*엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다. This will cause values to be silently truncated if they do not fit in the default precision and scale. 'HasColumnType()'을 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다.* " 이 경고는 무시할 수 있으며 이후 자습서에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-299">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98243-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-300">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="98243-301">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="98243-301">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="98243-302">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-302">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="98243-303">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-303">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="98243-304">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-304">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="98243-305">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="98243-305">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="98243-306">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-306">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="98243-307">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-307">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="98243-308">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="98243-308">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="98243-309">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-309">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="98243-310">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-310">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="98243-311">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-311">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="98243-312">이전 코드에서는 엔터티 집합에 대한 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98243-312">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="98243-313">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-313">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="98243-314">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-314">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="98243-315">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="98243-315">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="98243-316">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-316">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98243-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98243-317">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="98243-318">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-318">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98243-319">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98243-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="98243-320">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-320">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="98243-321">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-321">Test the app</span></span>

* <span data-ttu-id="98243-322">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="98243-322">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="98243-323">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="98243-323">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="98243-324">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-324">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="98243-325">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-325">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="98243-327">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98243-327">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="98243-328">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-328">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="98243-329">세계화 지침은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="98243-329">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="98243-330">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-330">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="98243-331">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="98243-331">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98243-332">추가 자료</span><span class="sxs-lookup"><span data-stu-id="98243-332">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="98243-333">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="98243-333">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
