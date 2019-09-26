---
title: ASP.NET Core에서 Razor 페이지 앱에 모델 추가
author: rick-anderson
description: Entity Framework Core(EF Core)를 사용하여 데이터베이스에서 영화를 관리하기 위한 클래스를 추가하는 방법을 알아봅니다.
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: dcbcf37dfd95d784ebe249ec6e9e4184a8853d3d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187190"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="dafc4-103">ASP.NET Core에서 Razor 페이지 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="dafc4-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="dafc4-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dafc4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dafc4-105">이 섹션에서는 데이터베이스에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="dafc4-106">이러한 클래스를 EF Core([Entity Framework Core](/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="dafc4-107">EF Core는 데이터 액세스를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="dafc4-108">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dafc4-109">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="dafc4-110">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="dafc4-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafc4-112">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dafc4-113">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-113">Name the folder *Models*.</span></span>

<span data-ttu-id="dafc4-114">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-114">Right click the *Models* folder.</span></span> <span data-ttu-id="dafc4-115">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="dafc4-116">클래스의 이름을 **동영상**으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dafc4-118">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dafc4-119">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-120">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dafc4-121">솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dafc4-122">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="dafc4-123">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="dafc4-124">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="dafc4-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dafc4-125">왼쪽 창에서 **일반**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dafc4-126">가운데 창에서 **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dafc4-127">클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="dafc4-128">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dafc4-129">동영상 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="dafc4-129">Scaffold the movie model</span></span>

<span data-ttu-id="dafc4-130">이 섹션에서는 동영상 모델을 스캐폴 합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dafc4-131">즉, 스캐폴드 도구는 동영상 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafc4-133">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dafc4-134">*페이지* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dafc4-135">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-135">Name the folder *Movies*</span></span>

<span data-ttu-id="dafc4-136">*페이지/동영상* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="dafc4-138">**스캐폴드 추가** 대화 상자에서 **Entity Framework(CRUD)를 사용한 Razor Pages** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="dafc4-140">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dafc4-141">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="dafc4-142">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름을 RazorPagesMovie.**Models**.RazorPagesMovieContext에서 RazorPagesMovie.**Data**.RazorPagesMovieContext로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="dafc4-143">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="dafc4-144">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="dafc4-145">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-145">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/3/arp.png)

<span data-ttu-id="dafc4-147">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="dafc4-149">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dafc4-150">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="dafc4-151">**Windows의 경우**: 다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="dafc4-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dafc4-152">**macOS 및 Linux의 경우**: 다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="dafc4-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-153">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dafc4-154">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dafc4-155">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="dafc4-156">다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="dafc4-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="dafc4-157">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="dafc4-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafc4-159">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dafc4-160">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="dafc4-161">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dafc4-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dafc4-162">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="dafc4-162">Updated</span></span>

* <span data-ttu-id="dafc4-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dafc4-163">*Startup.cs*</span></span>

<span data-ttu-id="dafc4-164">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="dafc4-165">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dafc4-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dafc4-166">스캐폴드 프로세스는 다음 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="dafc4-167">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="dafc4-168">생성된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dafc4-169">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="dafc4-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafc4-171">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dafc4-172">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-172">Add an initial migration.</span></span>
* <span data-ttu-id="dafc4-173">초기 마이그레이션을 사용하여 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="dafc4-174">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dafc4-176">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-178">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="dafc4-179">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dafc4-180">그러면 값이 기본 전체 자릿수 및 소수 자릿수에 적합하지 않은 경우 자동으로 잘립니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dafc4-181">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="dafc4-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dafc4-182">해당 경고를 무시할 수 있지만 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="dafc4-183">`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-183">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dafc4-184">스키마는 `DbContext`에 지정된 모델을 기반으로 합니다(*RazorPagesMovieContext.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="dafc4-184">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dafc4-185">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="dafc4-186">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="dafc4-187">`ef database update` 명령은 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-187">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="dafc4-188">`Up` 메서드는 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-188">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dafc4-190">종속성 주입을 사용하여 등록된 컨텍스트 검사</span><span class="sxs-lookup"><span data-stu-id="dafc4-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dafc4-191">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dafc4-192">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dafc4-193">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dafc4-194">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dafc4-195">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dafc4-196">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dafc4-197">강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="dafc4-198">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(만들기, 읽기, 업데이트, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="dafc4-199">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dafc4-200">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="dafc4-201">이전 코드에서는 엔터티 집합에 대한 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dafc4-202">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dafc4-203">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dafc4-204">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dafc4-205">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dafc4-207">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-208">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dafc4-209">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-209">Examine the `Up` method.</span></span>

---

<span data-ttu-id="dafc4-210">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-210">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dafc4-211">스키마는 `RazorPagesMovieContext`에 지정된 모델을 기반으로 합니다(*Data/RazorPagesMovieContext.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="dafc4-211">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dafc4-212">`Initial` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-212">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="dafc4-213">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-213">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="dafc4-214">자세한 내용은 <xref:data/ef-mvc/migrations>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dafc4-214">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="dafc4-215">`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/{time-stamp}_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-215">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dafc4-216">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="dafc4-216">Test the app</span></span>

* <span data-ttu-id="dafc4-217">앱을 실행하고 브라우저에서 `/Movies`를 URL에 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dafc4-217">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dafc4-218">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="dafc4-218">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dafc4-219">[마이그레이션 단계](#pmc)를 누락했습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-219">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dafc4-220">**만들기** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-220">Test the **Create** link.</span></span>

  ![만들기 페이지](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="dafc4-222">`Price` 필드에는 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-222">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dafc4-223">소수점으로 쉼표(",")를 사용하는 영어가 아닌 로캘 및 미국 영어가 아닌 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 글로벌화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-223">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dafc4-224">세계화 지침은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dafc4-224">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dafc4-225">**편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-225">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="dafc4-226">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-226">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dafc4-227">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="dafc4-227">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dafc4-228">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="dafc4-228">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dafc4-229">이 섹션에서는 데이터베이스에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-229">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="dafc4-230">이러한 클래스를 EF Core([Entity Framework Core](/ef/core))와 함께 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-230">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="dafc4-231">EF Core는 데이터 액세스 코드를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-231">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="dafc4-232">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-232">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dafc4-233">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-233">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="dafc4-234">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="dafc4-234">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafc4-236">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-236">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dafc4-237">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-237">Name the folder *Models*.</span></span>

<span data-ttu-id="dafc4-238">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-238">Right click the *Models* folder.</span></span> <span data-ttu-id="dafc4-239">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-239">Select **Add** > **Class**.</span></span> <span data-ttu-id="dafc4-240">클래스의 이름을 **동영상**으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-240">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-241">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-241">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dafc4-242">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-242">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dafc4-243">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-243">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-244">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-244">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dafc4-245">솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-245">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dafc4-246">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-246">Name the folder *Models*.</span></span>
* <span data-ttu-id="dafc4-247">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-247">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="dafc4-248">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="dafc4-248">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dafc4-249">왼쪽 창에서 **일반**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-249">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dafc4-250">가운데 창에서 **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-250">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dafc4-251">클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-251">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="dafc4-252">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-252">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dafc4-253">동영상 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="dafc4-253">Scaffold the movie model</span></span>

<span data-ttu-id="dafc4-254">이 섹션에서는 동영상 모델을 스캐폴 합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-254">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dafc4-255">즉, 스캐폴드 도구는 동영상 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-255">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafc4-257">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-257">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dafc4-258">*페이지* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-258">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dafc4-259">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-259">Name the folder *Movies*</span></span>

<span data-ttu-id="dafc4-260">*페이지/동영상* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-260">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="dafc4-262">**스캐폴드 추가** 대화 상자에서 **Entity Framework(CRUD)를 사용한 Razor Pages** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-262">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="dafc4-264">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-264">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="dafc4-265">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-265">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="dafc4-266">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름인 **RazorPagesMovie.Models.RazorPagesMovieContext**를 수용합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-266">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="dafc4-267">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-267">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arp.png)

<span data-ttu-id="dafc4-269">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-269">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="dafc4-271">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dafc4-272">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-272">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="dafc4-273">**Windows의 경우**: 다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="dafc4-273">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dafc4-274">**macOS 및 Linux의 경우**: 다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="dafc4-274">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-275">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-275">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dafc4-276">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-276">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dafc4-277">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-277">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="dafc4-278">다음 명령 실행:</span><span class="sxs-lookup"><span data-stu-id="dafc4-278">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="dafc4-279">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-279">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="dafc4-280">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="dafc4-280">Files created</span></span>

* <span data-ttu-id="dafc4-281">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-281">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="dafc4-282">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dafc4-282">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="dafc4-283">파일 업데이트됨</span><span class="sxs-lookup"><span data-stu-id="dafc4-283">File updated</span></span>

* <span data-ttu-id="dafc4-284">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dafc4-284">*Startup.cs*</span></span>

<span data-ttu-id="dafc4-285">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-285">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dafc4-286">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="dafc4-286">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-287">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafc4-288">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-288">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dafc4-289">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-289">Add an initial migration.</span></span>
* <span data-ttu-id="dafc4-290">초기 마이그레이션을 사용하여 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-290">Update the database with the initial migration.</span></span>

<span data-ttu-id="dafc4-291">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-291">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dafc4-293">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-293">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-294">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-294">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-295">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="dafc4-296">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-296">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dafc4-297">그러면 값이 기본 전체 자릿수 및 소수 자릿수에 적합하지 않은 경우 자동으로 잘립니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-297">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dafc4-298">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="dafc4-298">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dafc4-299">해당 경고를 무시할 수 있지만 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-299">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="dafc4-300">`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-300">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dafc4-301">스키마는 `DbContext`에 지정된 모델을 기반으로 합니다(*RazorPagesMovieContext.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="dafc4-301">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dafc4-302">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-302">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="dafc4-303">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-303">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="dafc4-304">`ef database update` 명령은 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-304">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="dafc4-305">`Up` 메서드는 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-305">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafc4-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-306">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dafc4-307">종속성 주입을 사용하여 등록된 컨텍스트 검사</span><span class="sxs-lookup"><span data-stu-id="dafc4-307">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dafc4-308">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-308">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dafc4-309">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-309">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dafc4-310">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-310">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dafc4-311">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-311">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dafc4-312">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-312">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dafc4-313">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-313">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dafc4-314">강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-314">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="dafc4-315">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(만들기, 읽기, 업데이트, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-315">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="dafc4-316">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-316">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dafc4-317">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-317">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="dafc4-318">이전 코드에서는 엔터티 집합에 대한 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-318">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dafc4-319">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-319">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dafc4-320">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-320">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dafc4-321">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-321">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dafc4-322">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-322">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafc4-323">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafc4-323">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dafc4-324">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-324">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dafc4-325">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafc4-325">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dafc4-326">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-326">Examine the `Up` method.</span></span>

---

<span data-ttu-id="dafc4-327">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-327">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dafc4-328">스키마는 `RazorPagesMovieContext`에 지정된 모델을 기반으로 합니다(*Data/RazorPagesMovieContext.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="dafc4-328">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dafc4-329">`Initial` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-329">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="dafc4-330">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-330">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="dafc4-331">자세한 내용은 <xref:data/ef-mvc/migrations>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dafc4-331">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="dafc4-332">`Update-Database` 명령은 데이터베이스를 만드는 *Migrations/{time-stamp}_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-332">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dafc4-333">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="dafc4-333">Test the app</span></span>

* <span data-ttu-id="dafc4-334">앱을 실행하고 브라우저에서 `/Movies`를 URL에 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dafc4-334">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dafc4-335">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="dafc4-335">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dafc4-336">[마이그레이션 단계](#pmc)를 누락했습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-336">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dafc4-337">**만들기** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-337">Test the **Create** link.</span></span>

  ![만들기 페이지](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="dafc4-339">`Price` 필드에는 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-339">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dafc4-340">소수점으로 쉼표(",")를 사용하는 영어가 아닌 로캘 및 미국 영어가 아닌 날짜 형식에 대해 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 글로벌화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-340">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dafc4-341">세계화 지침은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dafc4-341">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dafc4-342">**편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-342">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="dafc4-343">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dafc4-343">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dafc4-344">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="dafc4-344">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dafc4-345">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="dafc4-345">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
