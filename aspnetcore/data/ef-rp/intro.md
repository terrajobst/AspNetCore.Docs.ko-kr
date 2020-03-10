---
title: ASP.NET Core에서 Entity Framework Core를 사용한 Razor 페이지 - 자습서 1/8
author: rick-anderson
description: Entity Framework Core를 사용하여 Razor 페이지 앱을 만드는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78645801"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="64ef5-103">ASP.NET Core에서 Entity Framework Core를 사용한 Razor 페이지 - 자습서 1/8</span><span class="sxs-lookup"><span data-stu-id="64ef5-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="64ef5-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64ef5-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64ef5-105">[ASP.NET Core Razor Pages](xref:razor-pages/index) 앱에서 EF(Entity Framework) Core를 사용하는 방법을 보여 주는 일련의 자습서 중 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="64ef5-106">이 자습서에서는 가상 Contoso 대학의 웹 사이트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="64ef5-107">이 사이트에는 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="64ef5-108">이 자습서에서는 Code First 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="64ef5-109">Database First 방법을 사용하여 이 자습서를 수행하는 데 대한 정보는 [이 Github 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16897)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="64ef5-110">완성된 앱을 다운로드하거나 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="64ef5-111">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="64ef5-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64ef5-112">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="64ef5-112">Prerequisites</span></span>

* <span data-ttu-id="64ef5-113">Razor Pages를 처음 사용하는 경우 이 자습서를 시작하기 전에 [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) 자습서 시리즈를 진행하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="64ef5-116">데이터베이스 엔진</span><span class="sxs-lookup"><span data-stu-id="64ef5-116">Database engines</span></span>

<span data-ttu-id="64ef5-117">Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="64ef5-118">Visual Studio Code 지침에서는 플랫폼 간 데이터베이스 엔진인 [SQLite](https://www.sqlite.org/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="64ef5-119">SQLite를 사용하도록 선택하는 경우 [SQLite용 DB 브라우저](https://sqlitebrowser.org/)와 같이 SQLite 데이터베이스를 관리하고 볼 수 있는 타사 도구를 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="64ef5-120">문제 해결</span><span class="sxs-lookup"><span data-stu-id="64ef5-120">Troubleshooting</span></span>

<span data-ttu-id="64ef5-121">해결할 수 없는 문제가 발생한 경우 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="64ef5-122">도움을 얻으려면 [ASP.NET Core 태그](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core 태그](https://stackoverflow.com/questions/tagged/entity-framework-core)를 사용하여 StackOverflow.com에 질문을 게시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="64ef5-123">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="64ef5-123">The sample app</span></span>

<span data-ttu-id="64ef5-124">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="64ef5-125">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="64ef5-126">자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-126">Here are a few of the screens created in the tutorial.</span></span>

![학생 인덱스 페이지](intro/_static/students-index30.png)

![학생 편집 페이지](intro/_static/student-edit30.png)

<span data-ttu-id="64ef5-129">이 사이트의 UI 스타일은 기본 제공 프로젝트 템플릿을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="64ef5-130">이 자습서에서는 UI를 사용자 지정하는 방법이 아닌 EF Core를 사용하는 방법을 중점적으로 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="64ef5-131">페이지 맨 위에 있는 링크를 따라 완료된 프로젝트의 소스 코드를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="64ef5-132">*cu30* 폴더에는 자습서의 ASP.NET Core 3.0 버전에 대한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="64ef5-133">자습서 1~7의 코드 상태를 반영하는 파일은 *cu30snapshots* 폴더에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ef5-135">완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="64ef5-136">이름에 *SQLite*가 있는 파일 3개 및 폴더 1개를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="64ef5-137">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-137">Build the project.</span></span>
* <span data-ttu-id="64ef5-138">PMC(패키지 관리자 콘솔)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="64ef5-139">프로젝트를 실행하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64ef5-141">완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="64ef5-142">*ContosoUniversity.csproj*를 삭제하고 *ContosoUniversitySQLite.csproj*의 이름을 *ContosoUniversity.csproj*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="64ef5-143">*Startup.cs*를 삭제하고 *StartupSQLite.cs*의 이름을 *Startup.cs*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="64ef5-144">*appSettings.json*을 삭제하고 *appSettingsSQLite.json*의 이름을 *appSettings.json*으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="64ef5-145">*Migrations* 폴더를 삭제하고 *MigrationsSQL*의 이름을 *Migrations*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="64ef5-146">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-146">Build the project.</span></span>
* <span data-ttu-id="64ef5-147">프로젝트 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="64ef5-148">SQLite 도구에서 다음 SQL 문을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="64ef5-149">프로젝트를 실행하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="64ef5-150">웹앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="64ef5-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64ef5-152">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="64ef5-153">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="64ef5-154">프로젝트 이름을 *ContosoUniversity*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="64ef5-155">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 대문자 표시를 포함하여 정확한 이름을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="64ef5-156">드롭다운에서 **.NET Core** 및 **ASP.NET Core 3.0**을 선택한 후 **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="64ef5-158">터미널에서 프로젝트 폴더를 만들어야 하는 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="64ef5-159">다음 명령을 실행하여 Razor Pages 프로젝트를 만들고 `cd`를 사용하여 새 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="64ef5-160">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="64ef5-160">Set up the site style</span></span>

<span data-ttu-id="64ef5-161">*Pages/Shared/_Layout.cshtml*을 업데이트하여 사이트 헤더, 바닥글 및 메뉴를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="64ef5-162">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="64ef5-163">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-163">There are three occurrences.</span></span>

* <span data-ttu-id="64ef5-164">**홈** 및 **프라이버시** 메뉴 항목을 삭제하고 **정보**, **학생**, **과정**, **강사** 및 **부서** 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="64ef5-165">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="64ef5-166">*Pages/Index.cshtml*에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET Core에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="64ef5-167">앱을 실행하여 홈페이지가 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="64ef5-168">데이터 모델</span><span class="sxs-lookup"><span data-stu-id="64ef5-168">The data model</span></span>

<span data-ttu-id="64ef5-169">다음 섹션에서는 데이터 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-169">The following sections create a data model:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="64ef5-171">학생은 개수와 관계없이 원하는 과정에 등록할 수 있으며 한 과정에는 여러 명이 등록될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="64ef5-172">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="64ef5-172">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

* <span data-ttu-id="64ef5-174">프로젝트 폴더에 *Models* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="64ef5-175">다음 코드로 *Models/Student.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="64ef5-176">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="64ef5-177">기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="64ef5-178">따라서 `Student` 클래스 기본 키의 자동으로 인식되는 대체 이름은 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="64ef5-179">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="64ef5-180">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="64ef5-181">이 경우 `Student` 엔터티의 `Enrollments` 속성은 해당 Student에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="64ef5-182">예를 데이터베이스의 Student 행에 두 개의 관련 Enrollment 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 Enrollment 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="64ef5-183">데이터베이스에서 StudentID 열에 학생 ID 값이 포함된 경우 Enrollment 행은 Student 행과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="64ef5-184">예를 들어 Student 행에 ID=1이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="64ef5-185">관련 Enrollment 행에는 StudentID=1이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="64ef5-186">StudentID는 Enrollment 테이블의 ‘외래 키’입니다. </span><span class="sxs-lookup"><span data-stu-id="64ef5-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="64ef5-187">관련 Enrollment 엔터티가 여러 개 있을 수 있으므로 `Enrollments` 속성은 `ICollection<Enrollment>`로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="64ef5-188">`List<Enrollment>` 또는`HashSet<Enrollment>`와 같은 다른 컬렉션 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="64ef5-189">`ICollection<Enrollment>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<Enrollment>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="64ef5-190">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="64ef5-190">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="64ef5-192">다음 코드로 *Models/Enrollment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="64ef5-193">`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="64ef5-194">프로덕션 데이터 모델의 경우 하나의 패턴을 선택하고 일관되게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="64ef5-195">이 자습서에서는 두 항목이 작동하는 것을 보여 주기 위해 둘 다 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="64ef5-196">`classname` 없이 `ID`를 사용하면 특정 종류의 데이터 모델 변경 내용을 더 쉽게 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="64ef5-197">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="64ef5-198">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/)이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="64ef5-199">Null인 등급은 0등급과는 다릅니다. Null은 알 수 없거나 아직 할당되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="64ef5-200">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="64ef5-201">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="64ef5-202">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="64ef5-203">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="64ef5-204">EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="64ef5-205">예를 들어 `Student` 엔터티의 기본 키는 `ID`이므로 `StudentID`는 `Student` 탐색 속성의 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="64ef5-206">외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="64ef5-207">예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="64ef5-208">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="64ef5-208">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="64ef5-210">다음 코드로 *Models/Course.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="64ef5-211">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="64ef5-212">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="64ef5-213">`DatabaseGenerated` 특성을 사용하면 데이터베이스에서 기본 키를 생성하도록 하지 않고 앱에서 기본 키를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="64ef5-214">프로젝트를 빌드하여 컴파일러 오류가 없는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="64ef5-215">학생 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="64ef5-215">Scaffold Student pages</span></span>

<span data-ttu-id="64ef5-216">이 섹션에서는 ASP.NET Core 스캐폴딩 도구를 사용하여 다음을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="64ef5-217">EF Core ‘컨텍스트’ 클래스. </span><span class="sxs-lookup"><span data-stu-id="64ef5-217">An EF Core *context* class.</span></span> <span data-ttu-id="64ef5-218">컨텍스트는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="64ef5-219">이 클래스는 `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="64ef5-220">`Student` 엔터티에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리하는 Razor 페이지.</span><span class="sxs-lookup"><span data-stu-id="64ef5-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64ef5-222">*Pages* 폴더에 *Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="64ef5-223">**솔루션 탐색기**에서 *Pages/Students* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="64ef5-224">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="64ef5-225">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="64ef5-226">**모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="64ef5-227">**데이터 컨텍스트 클래스** 행에서 **+** (더하기) 기호를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="64ef5-228">데이터 컨텍스트 이름을 *ContosoUniversity.Models.ContosoUniversityContext*에서 *ContosoUniversity.Data.SchoolContext*로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="64ef5-229">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-229">Select **Add**.</span></span>

<span data-ttu-id="64ef5-230">다음 패키지가 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="64ef5-232">다음 .NET Core CLI 명령을 실행하여 필요한 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="64ef5-233">스캐폴딩에는 Microsoft.VisualStudio.Web.CodeGeneration.Design 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="64ef5-234">앱은 SQL Server를 사용하지 않지만 스캐폴딩 도구에는 SQL Server 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="64ef5-235">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="64ef5-236">다음 명령을 실행하여 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="64ef5-237">다음 명령을 실행하여 학생 페이지를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="64ef5-238">**Windows**</span><span class="sxs-lookup"><span data-stu-id="64ef5-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="64ef5-239">**macOS 또는 Linux**</span><span class="sxs-lookup"><span data-stu-id="64ef5-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="64ef5-240">이전 단계에서 문제가 발생하면 프로젝트를 빌드하고 스캐폴드 단계를 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="64ef5-241">스캐폴딩 프로세스:</span><span class="sxs-lookup"><span data-stu-id="64ef5-241">The scaffolding process:</span></span>

* <span data-ttu-id="64ef5-242">*Pages/Students* 폴더에서 다음 Razor 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="64ef5-243">*Create.cshtml* 및 *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="64ef5-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="64ef5-244">*Delete.cshtml* 및 *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="64ef5-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="64ef5-245">*Details.cshtml* 및 *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="64ef5-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="64ef5-246">*Edit.cshtml* 및 *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="64ef5-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="64ef5-247">*Index.cshtml* 및 *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="64ef5-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="64ef5-248">*Data/SchoolContext.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="64ef5-249">*Startup.cs*의 종속성 주입에 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="64ef5-250">*appsettings.json*에 데이터베이스 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="64ef5-251">데이터베이스 연결 문자열</span><span class="sxs-lookup"><span data-stu-id="64ef5-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ef5-253">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="64ef5-254">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="64ef5-255">기본적으로 LocalDB는 `C:/Users/<user>` 디렉터리에 *.mdf* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64ef5-257">*CU.db*라는 SQLite 데이터베이스 파일을 가리키도록 연결 문자열을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="64ef5-258">데이터베이스 컨텍스트 클래스 업데이트</span><span class="sxs-lookup"><span data-stu-id="64ef5-258">Update the database context class</span></span>

<span data-ttu-id="64ef5-259">특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="64ef5-260">컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="64ef5-261">컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="64ef5-262">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="64ef5-263">*SchoolContext.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="64ef5-264">강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="64ef5-265">EF Core 용어에서:</span><span class="sxs-lookup"><span data-stu-id="64ef5-265">In EF Core terminology:</span></span>

* <span data-ttu-id="64ef5-266">엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="64ef5-267">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="64ef5-268">엔터티 집합은 여러 엔터티를 포함하므로 DBSet 속성은 복수형 이름이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="64ef5-269">스캐폴딩 도구로 `Student` DBSet를 만들었으므로 이 단계에서는 이 DBSet를 복수형 `Students`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="64ef5-270">Razor Pages 코드가 새 DBSet 이름과 일치하게 만들려면 전체 프로젝트에서 `_context.Student`를 `_context.Students`로 전역으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="64ef5-271">8개 발생 항목이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-271">There are 8 occurrences.</span></span>

<span data-ttu-id="64ef5-272">프로젝트를 빌드하여 컴파일러 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="64ef5-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="64ef5-273">Startup.cs</span></span>

<span data-ttu-id="64ef5-274">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="64ef5-275">서비스(예: EF Core 데이터베이스 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="64ef5-276">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="64ef5-277">데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="64ef5-278">스캐폴딩 도구는 종속성 주입 컨테이너에 컨텍스트 클래스를 자동으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64ef5-280">`ConfigureServices`에서 강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="64ef5-282">`ConfigureServices`에서 스캐폴더에서 추가된 코드가 `UseSqlite`를 호출하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="64ef5-283">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="64ef5-284">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="64ef5-285">데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="64ef5-285">Create the database</span></span>

<span data-ttu-id="64ef5-286">*Program.cs*를 업데이트하여 데이터베이스가 없는 경우 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="64ef5-287">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 메서드는 컨텍스트의 데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="64ef5-288">데이터베이스가 없는 경우 데이터베이스 및 스키마를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="64ef5-289">`EnsureCreated`를 사용하면 다음 워크플로가 데이터 모델 변경 내용을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="64ef5-290">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-290">Delete the database.</span></span> <span data-ttu-id="64ef5-291">모든 기존 데이터가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-291">Any existing data is lost.</span></span>
* <span data-ttu-id="64ef5-292">데이터 모델을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-292">Change the data model.</span></span> <span data-ttu-id="64ef5-293">예를 들어 `EmailAddress` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="64ef5-294">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-294">Run the app.</span></span>
* <span data-ttu-id="64ef5-295">`EnsureCreated`는 새 스키마를 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="64ef5-296">이 워크플로는 데이터를 보존할 필요가 없는 경우 스키마가 빠르게 업데이트되는 개발 초기에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="64ef5-297">데이터베이스에 입력된 데이터를 보존해야 하는 경우는 상황이 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="64ef5-298">이 경우에는 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="64ef5-299">자습서 시리즈의 뒷부분에서는 `EnsureCreated`에서 만든 데이터베이스를 삭제하고 마이그레이션을 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="64ef5-300">`EnsureCreated`에서 만든 데이터베이스는 마이그레이션을 사용하여 업데이트할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="64ef5-301">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-301">Test the app</span></span>

* <span data-ttu-id="64ef5-302">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-302">Run the app.</span></span>
* <span data-ttu-id="64ef5-303">**학생** 링크 및 **새로 만들기**를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="64ef5-304">편집, 세부 정보 및 삭제 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="64ef5-305">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="64ef5-305">Seed the database</span></span>

<span data-ttu-id="64ef5-306">`EnsureCreated` 메서드는 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="64ef5-307">이 섹션에서는 테스트 데이터로 데이터베이스를 채우는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="64ef5-308">다음 코드로 *Data/DbInitializer.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="64ef5-309">이 코드는 데이터베이스에 학생이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="64ef5-310">학생이 없는 경우 테스트 데이터를 데이터베이스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="64ef5-311">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 만들어 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="64ef5-312">*Program.cs*에서 `EnsureCreated` 호출을 `DbInitializer.Initialize` 호출로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ef5-314">앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="64ef5-316">실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="64ef5-317">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-317">Restart the app.</span></span>

* <span data-ttu-id="64ef5-318">학생 페이지를 선택하여 시드된 데이터를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="64ef5-319">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="64ef5-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64ef5-321">Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="64ef5-322">SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="64ef5-323">데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="64ef5-324">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="64ef5-325">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기**를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="64ef5-326">**Student** 테이블을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭하여 `Student` 모델이 `Student` 테이블 스키마에 어떻게 매핑되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64ef5-328">SQLite 도구를 사용하여 데이터베이스 스키마 및 시드된 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="64ef5-329">데이터베이스 파일은 *CU.db*로 이름이 지정되고 프로젝트 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="64ef5-330">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="64ef5-330">Asynchronous code</span></span>

<span data-ttu-id="64ef5-331">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="64ef5-332">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="64ef5-333">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="64ef5-334">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="64ef5-335">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="64ef5-336">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="64ef5-337">비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="64ef5-338">트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="64ef5-339">다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="64ef5-340">`async` 키워드는 컴파일러가 다음을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="64ef5-341">메서드 본문의 부분에 대한 콜백을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="64ef5-342">반환되는 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="64ef5-343">`Task<T>` 반환 형식은 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="64ef5-344">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="64ef5-345">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="64ef5-346">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="64ef5-347">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="64ef5-348">EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="64ef5-349">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="64ef5-350">여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="64ef5-351">`var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="64ef5-352">EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="64ef5-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="64ef5-353">비동기 코드의 성능 이점을 이용하려면 라이브러리 패키지(예: 페이징)가 쿼리를 데이터베이스에 보내는 EF Core 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="64ef5-354">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="64ef5-355">다음 단계</span><span class="sxs-lookup"><span data-stu-id="64ef5-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="64ef5-356">다음 자습서</span><span class="sxs-lookup"><span data-stu-id="64ef5-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="64ef5-357">Contoso University 샘플 웹앱은 EF(Entity Framework) Core를 사용하여 ASP.NET Core Razor Pages 앱을 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="64ef5-358">샘플 앱은 가상 Contoso University의 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="64ef5-359">학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="64ef5-360">이 페이지는 Contoso University 샘플 앱을 빌드하는 방법을 설명하는 일련의 자습서 중 첫 번째 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="64ef5-361">완성된 앱을 다운로드하거나 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="64ef5-362">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="64ef5-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64ef5-363">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="64ef5-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="64ef5-366">[Razor 페이지](xref:razor-pages/index)에 익숙함.</span><span class="sxs-lookup"><span data-stu-id="64ef5-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="64ef5-367">신규 프로그래머는 이 시리즈를 시작하기 전에 [Razor 페이지 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 완료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="64ef5-368">문제 해결</span><span class="sxs-lookup"><span data-stu-id="64ef5-368">Troubleshooting</span></span>

<span data-ttu-id="64ef5-369">해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="64ef5-370">도움을 얻으려면 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)에 질문을 게시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="64ef5-371">Contoso University 웹앱</span><span class="sxs-lookup"><span data-stu-id="64ef5-371">The Contoso University web app</span></span>

<span data-ttu-id="64ef5-372">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="64ef5-373">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="64ef5-374">자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-374">Here are a few of the screens created in the tutorial.</span></span>

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

<span data-ttu-id="64ef5-377">이 사이트의 UI 스타일은 기본 제공 템플릿에서 생성된 것과 가깝습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="64ef5-378">자습서는 UI가 아닌 Razor 페이지를 사용한 EF Core 자습서 위주로 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="64ef5-379">ContosoUniversity Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="64ef5-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64ef5-381">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="64ef5-382">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="64ef5-383">프로젝트 이름을 **ContosoUniversity**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="64ef5-384">코드를 복사/붙여넣을 때 네임스페이스와 일치할 수 있도록 프로젝트 이름을 *ContosoUniversity*로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="64ef5-385">드롭다운에서 **ASP.NET Core 2.1**을 선택한 다음, **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="64ef5-386">이전 단계의 이미지는 [Razor 웹앱 만들기](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="64ef5-387">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="64ef5-389">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="64ef5-389">Set up the site style</span></span>

<span data-ttu-id="64ef5-390">몇 가지 변경 내용으로 사이트 메뉴, 레이아웃 및 홈페이지를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="64ef5-391">*Pages/Shared/_Layout.cshtml*을 다음 변경 내용으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="64ef5-392">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="64ef5-393">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-393">There are three occurrences.</span></span>

* <span data-ttu-id="64ef5-394">**학생**, **강좌**, **강사** 및 **부서**에 대한 메뉴 항목을 추가하고 **연락처** 메뉴 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="64ef5-395">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-395">The changes are highlighted.</span></span> <span data-ttu-id="64ef5-396">(모든 표시가 표시되지는 *않습니다*.)</span><span class="sxs-lookup"><span data-stu-id="64ef5-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="64ef5-397">*Pages/Index.cshtml*에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET 및 MVC에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="64ef5-398">데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="64ef5-398">Create the data model</span></span>

<span data-ttu-id="64ef5-399">Contoso University 앱에 대한 엔터티 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="64ef5-400">다음과 같은 세 가지 엔터티로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-400">Start with the following three entities:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="64ef5-402">`Student` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="64ef5-403">`Course` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="64ef5-404">학생은 여러 강좌에 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="64ef5-405">강좌는 등록된 학생이 여러 명일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="64ef5-406">다음 섹션에서 각각에 대한 이러한 엔터티에 대한 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="64ef5-407">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="64ef5-407">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

<span data-ttu-id="64ef5-409">*모델* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-409">Create a *Models* folder.</span></span> <span data-ttu-id="64ef5-410">*모델* 폴더에서 다음 코드로 *Student.cs*라는 이름의 클래스 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="64ef5-411">`ID` 속성은 이 클래스에 해당하는 DB(데이터베이스) 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="64ef5-412">기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="64ef5-413">`classnameID`에서 `classname`은 클래스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="64ef5-414">기본 키가 자동으로 인식되는 경우 대안은 앞의 예제에서 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="64ef5-415">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="64ef5-416">탐색 속성은 이 엔터티와 관련된 다른 엔터티에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="64ef5-417">이 경우 `Student entity`의 `Enrollments` 속성은 해당 `Student`에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="64ef5-418">예를 들어 DB의 학생 행에 두 개의 관련 등록 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="64ef5-419">관련된 `Enrollment` 행은 `StudentID` 열에서 해당 학생의 기본 키 값을 포함하는 열입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="64ef5-420">예를 들어 ID=1인 학생에 `Enrollment` 테이블의 두 개 행이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="64ef5-421">`Enrollment` 테이블에 `StudentID` = 1인 두 개의 행이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="64ef5-422">`StudentID`는 `Student` 테이블에서 학생을 지정하는 `Enrollment` 테이블의 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="64ef5-423">탐색 속성이 여러 엔터티를 포함하는 경우 탐색 속성은 `ICollection<T>`와 같은 목록 유형이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="64ef5-424">`ICollection<T>`는 지정할 수 있으며, `List<T>` 또는 `HashSet<T>`와 같은 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="64ef5-425">`ICollection<T>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="64ef5-426">여러 엔터티를 포함하는 탐색 속성은 다대다 및 일대다 관계에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="64ef5-427">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="64ef5-427">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="64ef5-429">*모델* 폴더에서 다음 코드로 *Enrollment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="64ef5-430">`EnrollmentID` 속성은 기본 키입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="64ef5-431">이 엔터티는 `Student` 엔터티 같은 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="64ef5-432">일반적으로 개발자는 하나의 패턴을 선택하여 데이터 모델 전체에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="64ef5-433">자습서의 뒷부분에서는 클래스 이름 없이 ID를 사용하여 더 손쉽게 데이터 모델에서 상속을 구현하는 내용이 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="64ef5-434">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="64ef5-435">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 nullable이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="64ef5-436">Null인 등급은 0 등급과는 다릅니다. Null은 알려지지 않거나 아직 등록되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="64ef5-437">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="64ef5-438">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="64ef5-439">`Student` 엔터티는 여러 `Enrollment` 엔터티를 포함하는 `Student.Enrollments` 탐색 속성과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="64ef5-440">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="64ef5-441">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="64ef5-442">EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="64ef5-443">예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 기본 키가 `ID`이므로 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="64ef5-444">외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="64ef5-445">예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="64ef5-446">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="64ef5-446">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="64ef5-448">*모델* 폴더에서 다음 코드로 *Course.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="64ef5-449">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="64ef5-450">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="64ef5-451">앱은 `DatabaseGenerated` 특성을 통해 DB가 생성하도록 하는 대신 기본 키를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="64ef5-452">학생 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="64ef5-452">Scaffold the student model</span></span>

<span data-ttu-id="64ef5-453">이 섹션에서는 학생 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="64ef5-454">즉, 스캐폴드 도구는 학생 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="64ef5-455">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-455">Build the project.</span></span>
* <span data-ttu-id="64ef5-456">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64ef5-458">**솔루션 탐색기**에서 *Pages/Students* 폴더 > **추가** > **스캐폴드 항목 새로 만들기**를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="64ef5-459">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="64ef5-460">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="64ef5-461">**모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="64ef5-462">**데이터 컨텍스트 클래스** 행에서 **+** (더하기)를 선택 하고 생성된 이름을 서명하고 **ContosoUniversity.Models.SchoolContext**로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="64ef5-463">**데이터 컨텍스트 클래스** 드롭다운에서 **ContosoUniversity.Models.SchoolContext**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="64ef5-464">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-464">Select **Add**.</span></span>

![CRUD 대화 상자](intro/_static/s1.png)

<span data-ttu-id="64ef5-466">이전 단계에서 문제가 발생한 경우 [영화 모델 스캐폴드](xref:tutorials/razor-pages/model#scaffold-the-movie-model)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64ef5-468">다음 명령을 실행하여 학생 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="64ef5-469">스캐폴드 프로세스는 다음 파일을 생성하고 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="64ef5-470">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="64ef5-470">Files created</span></span>

* <span data-ttu-id="64ef5-471">*Pages/Students* 만들기, 삭제, 세부 정보, 편집, 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="64ef5-472">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="64ef5-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="64ef5-473">파일 업데이트</span><span class="sxs-lookup"><span data-stu-id="64ef5-473">File updates</span></span>

* <span data-ttu-id="64ef5-474">*Startup.cs* : 이 파일의 변경 내용은 다음 섹션에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="64ef5-475">*appsettings.json* : 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="64ef5-476">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="64ef5-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="64ef5-477">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="64ef5-478">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="64ef5-479">이러한 서비스(예: Razor 페이지)가 필요한 구성 요소에는 생성자 매개 변수를 통해 이러한 서비스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="64ef5-480">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="64ef5-481">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="64ef5-482">*Startup.cs*의 `ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="64ef5-483">강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="64ef5-484">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="64ef5-485">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="64ef5-486">기본 업데이트</span><span class="sxs-lookup"><span data-stu-id="64ef5-486">Update main</span></span>

<span data-ttu-id="64ef5-487">*Program.cs*에서 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="64ef5-488">종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="64ef5-489">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="64ef5-490">`EnsureCreated` 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="64ef5-491">다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="64ef5-492">`EnsureCreated`는 컨텍스트에 대한 데이터베이스가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="64ef5-493">존재하는 경우 아무런 동작이 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-493">If it exists, no action is taken.</span></span> <span data-ttu-id="64ef5-494">존재하지 않는 경우 데이터베이스 및 해당하는 모든 스키마가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="64ef5-495">`EnsureCreated`는 데이터베이스를 만드는 데 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="64ef5-496">`EnsureCreated`를 사용하여 만든 데이터베이스는 나중에 마이그레이션을 사용하여 업데이트될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="64ef5-497">앱 시작 시 다음 작업 흐름을 허용하는 `EnsureCreated`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="64ef5-498">DB를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-498">Delete the DB.</span></span>
* <span data-ttu-id="64ef5-499">DB 스키마를 변경합니다(예: `EmailAddress` 필드 추가).</span><span class="sxs-lookup"><span data-stu-id="64ef5-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="64ef5-500">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-500">Run the app.</span></span>
* <span data-ttu-id="64ef5-501">`EnsureCreated`가 `EmailAddress` 열이 있는 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="64ef5-502">`EnsureCreated`는 스키마가 빠르게 발전하는 경우 개발 초기에 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="64ef5-503">나중에 자습서에서 DB가 삭제되고 마이그레이션이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="64ef5-504">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="64ef5-504">Test the app</span></span>

<span data-ttu-id="64ef5-505">앱을 실행하고 쿠키 방침에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="64ef5-506">이 앱은 개인 정보를 보관하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="64ef5-507">[EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)에서 쿠키 정책을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="64ef5-508">**학생** 링크 및 **새로 만들기**를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="64ef5-509">편집, 세부 정보 및 삭제 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="64ef5-510">SchoolContext DB 컨텍스트 검사</span><span class="sxs-lookup"><span data-stu-id="64ef5-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="64ef5-511">특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 DB 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="64ef5-512">데이터 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="64ef5-513">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="64ef5-514">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="64ef5-515">*SchoolContext.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="64ef5-516">강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="64ef5-517">EF Core 용어에서:</span><span class="sxs-lookup"><span data-stu-id="64ef5-517">In EF Core terminology:</span></span>

* <span data-ttu-id="64ef5-518">엔터티 집합은 일반적으로 DB 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="64ef5-519">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="64ef5-520">`DbSet<Enrollment>` 및 `DbSet<Course>`를 생략할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="64ef5-521">`Student` 엔터티는 `Enrollment` 엔터티를 참조하고 `Enrollment` 엔터티는 `Course` 엔터티를 참조하기 때문에 EF Core는 이를 암시적으로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="64ef5-522">이 자습서의 경우 `DbSet<Enrollment>` 및 `DbSet<Course>`를 `SchoolContext`에 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="64ef5-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="64ef5-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="64ef5-524">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="64ef5-525">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="64ef5-526">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="64ef5-527">기본적으로 LocalDB는 *.mdf* DB 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="64ef5-528">코드를 추가하여 테스트 데이터로 DB 초기화</span><span class="sxs-lookup"><span data-stu-id="64ef5-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="64ef5-529">EF Core가 빈 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="64ef5-530">이 섹션에서는 테스트 데이터로 채울 `Initialize` 메서드가 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="64ef5-531">*Data* 폴더에서 *DbInitializer.cs*라는 새 클래스 파일을 만들고 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="64ef5-532">참고: 앞의 코드는 `Data`가 아닌 `Models`을 네임스페이스(`namespace ContosoUniversity.Models`)에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="64ef5-533">`Models`는 스캐폴더에서 생성된 코드와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="64ef5-534">자세한 내용은 [이 GitHub 스캐폴딩 문제](https://github.com/aspnet/Scaffolding/issues/822)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="64ef5-535">코드는 DB에 학생이 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="64ef5-536">DB에 학생이 없는 경우 DB는 테스트 데이터로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="64ef5-537">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="64ef5-538">`EnsureCreated` 메서드는 자동으로 DB 컨텍스트에 대한 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="64ef5-539">DB가 있는 경우 `EnsureCreated`는 DB를 수정하지 않고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="64ef5-540">*Program.cs*에서 `Initialize`를 호출하도록 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="64ef5-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64ef5-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64ef5-542">앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="64ef5-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64ef5-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="64ef5-544">실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="64ef5-545">DB 보기</span><span class="sxs-lookup"><span data-stu-id="64ef5-545">View the DB</span></span>

<span data-ttu-id="64ef5-546">데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="64ef5-547">따라서 데이터베이스 이름은 “SchoolContext-{GUID}”입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="64ef5-548">GUID는 사용자에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="64ef5-549">Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="64ef5-550">SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="64ef5-551">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="64ef5-552">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기**를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="64ef5-553">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="64ef5-553">Asynchronous code</span></span>

<span data-ttu-id="64ef5-554">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="64ef5-555">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="64ef5-556">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="64ef5-557">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="64ef5-558">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="64ef5-559">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="64ef5-560">비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="64ef5-561">트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="64ef5-562">다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="64ef5-563">`async` 키워드는 컴파일러가 다음을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="64ef5-564">메서드 본문의 부분에 대한 콜백을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="64ef5-565">반환되는 [작업](/dotnet/api/system.threading.tasks.task) 개체를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="64ef5-566">자세한 내용은 [작업 반환 형식](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="64ef5-567">암시적 반환 형식 `Task`는 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="64ef5-568">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="64ef5-569">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="64ef5-570">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="64ef5-571">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="64ef5-572">EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="64ef5-573">쿼리 또는 명령을 DB에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="64ef5-574">여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="64ef5-575">`var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="64ef5-576">EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="64ef5-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="64ef5-577">비동기 코드의 성능 이점을 활용하려면 쿼리를 DB에 보내는 EF Core 메서드를 호출하는 경우 라이브러리 패키지(예: 페이징)가 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="64ef5-578">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="64ef5-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="64ef5-579">다음 자습서에서는 기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="64ef5-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="64ef5-580">추가 자료</span><span class="sxs-lookup"><span data-stu-id="64ef5-580">Additional resources</span></span>

* [<span data-ttu-id="64ef5-581">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="64ef5-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="64ef5-582">다음</span><span class="sxs-lookup"><span data-stu-id="64ef5-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
