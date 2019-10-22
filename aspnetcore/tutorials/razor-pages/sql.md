---
title: 데이터베이스 및 ASP.NET Core 작업
author: rick-anderson
description: 데이터베이스 및 ASP.NET Core 작업을 설명합니다.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334225"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="7a8ac-103">데이터베이스 및 ASP.NET Core 작업</span><span class="sxs-lookup"><span data-stu-id="7a8ac-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="7a8ac-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7a8ac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="7a8ac-105">`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="7a8ac-106">데이터베이스 컨텍스트는 *Startup.cs*의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7a8ac-108">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="7a8ac-109">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7a8ac-110">로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7a8ac-112">데이터베이스의 이름 값(`Database={Database name}`)은 생성된 코드에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="7a8ac-113">이름 값은 임의적입니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7a8ac-114">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="7a8ac-115">앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 실제 데이터베이스 서버로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="7a8ac-116">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="7a8ac-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="7a8ac-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7a8ac-119">LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="7a8ac-120">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7a8ac-121">기본적으로 LocalDB 데이터베이스는 `C:\Users\<user>\` 디렉터리에서 `*.mdf` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="7a8ac-122">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](sql/_static/ssox.png)

* <span data-ttu-id="7a8ac-124">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![동영상 테이블의 열린 바로 가기 메뉴](sql/_static/design.png)

  ![디자이너에서 열린 동영상 테이블](sql/_static/dv.png)

<span data-ttu-id="7a8ac-127">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="7a8ac-128">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="7a8ac-129">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![테이블 데이터를 보여 주는 열린 동영상 테이블](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7a8ac-131">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="7a8ac-132">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="7a8ac-132">Seed the database</span></span>

<span data-ttu-id="7a8ac-133">다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="7a8ac-134">DB에 동영상이 있는 경우 시드 이니셜라이저가 반환되고 동영상이 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="7a8ac-135">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="7a8ac-135">Add the seed initializer</span></span>

<span data-ttu-id="7a8ac-136">*Program.cs*에서 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7a8ac-137">종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7a8ac-138">컨텍스트를 전달하는 시드 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="7a8ac-139">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="7a8ac-140">다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="7a8ac-141">`Update-Database`가 실행되지 않은 경우 다음 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="7a8ac-142">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="7a8ac-142">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7a8ac-144">DB의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-144">Delete all the records in the DB.</span></span> <span data-ttu-id="7a8ac-145">브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="7a8ac-146">시드 메서드가 실행되도록 앱에서 초기화를 적용하도록 합니다(`Startup` 클래스에서 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="7a8ac-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="7a8ac-147">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="7a8ac-148">다음 접근 방식 중 하나를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="7a8ac-149">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지**를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * <span data-ttu-id="7a8ac-152">비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="7a8ac-153">디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7a8ac-154">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7a8ac-155">DB의 모든 레코드 삭제(시드 메서드 실행을 위해).</span><span class="sxs-lookup"><span data-stu-id="7a8ac-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7a8ac-156">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7a8ac-157">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="7a8ac-158">다음 자습서는 데이터의 표현을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a8ac-159">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7a8ac-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7a8ac-160">[이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="7a8ac-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="7a8ac-161">`RazorPagesMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="7a8ac-162">데이터베이스 컨텍스트는 *Startup.cs*의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7a8ac-164">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="7a8ac-165">`ConfigureServices`에서 사용되는 메서드에 대한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="7a8ac-166">`CookiePolicyOptions`에 대한 [EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="7a8ac-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="7a8ac-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="7a8ac-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="7a8ac-168">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="7a8ac-169">로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7a8ac-171">데이터베이스의 이름 값(`Database={Database name}`)은 생성된 코드에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="7a8ac-172">이름 값은 임의적입니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7a8ac-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a8ac-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7a8ac-174">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="7a8ac-175">앱이 테스트 또는 프로덕션 서버에 배포되는 경우 환경 변수를 사용하여 연결 문자열을 실제 데이터베이스 서버로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="7a8ac-176">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="7a8ac-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="7a8ac-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7a8ac-179">LocalDB는 프로그램 개발용으로 대상이 지정된 간단한 버전의 SQL Server Express 데이터베이스 엔진입니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="7a8ac-180">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7a8ac-181">기본적으로 LocalDB 데이터베이스는 `C:/Users/<user/>` 디렉터리에서 `*.mdf` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="7a8ac-182">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](sql/_static/ssox.png)

* <span data-ttu-id="7a8ac-184">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![동영상 테이블에서 열린 바로 가기 메뉴](sql/_static/design.png)

  ![디자이너에 열린 동영상 테이블](sql/_static/dv.png)

<span data-ttu-id="7a8ac-187">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="7a8ac-188">기본적으로 EF는 기본 키에 대해 `ID`라는 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="7a8ac-189">`Movie` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![테이블 데이터를 보여 주는 열린 동영상 테이블](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7a8ac-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a8ac-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7a8ac-192">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="7a8ac-193">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="7a8ac-193">Seed the database</span></span>

<span data-ttu-id="7a8ac-194">다음 코드를 사용하여 *Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="7a8ac-195">DB에 동영상이 있는 경우 시드 이니셜라이저가 반환되고 동영상이 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="7a8ac-196">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="7a8ac-196">Add the seed initializer</span></span>

<span data-ttu-id="7a8ac-197">*Program.cs*에서 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7a8ac-198">종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7a8ac-199">컨텍스트를 전달하는 시드 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="7a8ac-200">시드 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="7a8ac-201">다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="7a8ac-202">프로덕션 앱은 `Database.Migrate`를 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="7a8ac-203">`Update-Database`가 실행되지 않는 경우 다음 예외를 방지하기 위해 위의 코드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="7a8ac-204">SqlException: 로그인에서 요청한 “RazorPagesMovieContext-21” 데이터베이스를 열 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="7a8ac-205">로그인에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-205">The login failed.</span></span>
<span data-ttu-id="7a8ac-206">'user name' 사용자에 대한 로그인에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7a8ac-207">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="7a8ac-207">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7a8ac-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a8ac-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7a8ac-209">DB의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-209">Delete all the records in the DB.</span></span> <span data-ttu-id="7a8ac-210">브라우저 또는 [SSOX](xref:tutorials/razor-pages/new-field#ssox)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="7a8ac-211">시드 메서드가 실행되도록 앱에서 초기화를 적용하도록 합니다(`Startup` 클래스에서 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="7a8ac-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="7a8ac-212">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="7a8ac-213">다음 접근 방식 중 하나를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="7a8ac-214">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지**를 탭합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 시스템 트레이 아이콘](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](sql/_static/stopIIS.png)

    * <span data-ttu-id="7a8ac-217">비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="7a8ac-218">디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7a8ac-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a8ac-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7a8ac-220">DB의 모든 레코드 삭제(시드 메서드 실행을 위해).</span><span class="sxs-lookup"><span data-stu-id="7a8ac-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7a8ac-221">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7a8ac-222">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7a8ac-223">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7a8ac-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7a8ac-224">DB의 모든 레코드 삭제(시드 메서드 실행을 위해).</span><span class="sxs-lookup"><span data-stu-id="7a8ac-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="7a8ac-225">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="7a8ac-226">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="7a8ac-227">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-227">The app shows the seeded data:</span></span>

![동영상 데이터를 표시하는 크롬에서 열린 동영상 애플리케이션](sql/_static/m55.png)

<span data-ttu-id="7a8ac-229">다음 자습서는 데이터의 표현을 정리합니다.</span><span class="sxs-lookup"><span data-stu-id="7a8ac-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a8ac-230">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7a8ac-230">Additional resources</span></span>

* [<span data-ttu-id="7a8ac-231">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="7a8ac-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="7a8ac-232">[이전: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)
> [다음: 페이지 업데이트](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="7a8ac-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
