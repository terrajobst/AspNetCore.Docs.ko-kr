---
title: ASP.NET Core MVC 앱에서 SQL 작업
author: rick-anderson
description: ASP.NET Core MVC 앱에서 SQL Server LocalDB 또는 SQLite를 사용하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 983742276f3519b540cd62e4ada6eb5189650aa8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884118"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="94084-103">ASP.NET Core에서 SQL 작업</span><span class="sxs-lookup"><span data-stu-id="94084-103">Work with SQL in ASP.NET Core</span></span>

<span data-ttu-id="94084-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94084-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="94084-105">`MvcMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="94084-106">데이터베이스 컨텍스트는 *Startup.cs* 파일의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="94084-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94084-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94084-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="94084-108">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="94084-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="94084-109">로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="94084-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="94084-110">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94084-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="94084-111">ASP.NET Core [구성](xref:fundamentals/configuration/index) 시스템은 `ConnectionString`을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="94084-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="94084-112">로컬 개발의 경우 *appsettings.json* 파일에서 연결 문자열을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="94084-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="94084-113">테스트 또는 프로덕션 서버에 앱을 배포할 때 환경 변수 또는 다른 방법을 사용하여 실제 SQL Server에 연결 문자열을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94084-113">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="94084-114">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94084-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94084-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94084-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="94084-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="94084-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="94084-117">LocalDB는 프로그램 개발용으로 대상이 지정된 SQL Server Express 데이터베이스 엔진의 간단 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="94084-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="94084-118">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94084-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="94084-119">기본적으로 LocalDB 데이터베이스는 *C:/Users/{user}* 디렉터리에 *.mdf* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94084-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="94084-120">**보기** 메뉴에서 SSOX(**SQL Server 개체 탐색기**)를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="94084-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![보기 메뉴](working-with-sql/_static/ssox.png)

* <span data-ttu-id="94084-122">마우스 오른쪽 단추로 `Movie` 테이블 **> 디자이너 보기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![동영상 테이블에서 열린 바로 가기 메뉴](working-with-sql/_static/design.png)

  ![디자이너에 열린 동영상 테이블](working-with-sql/_static/dv.png)

<span data-ttu-id="94084-125">`ID` 옆의 키 아이콘을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="94084-126">기본적으로 EF는 `ID`라는 속성을 기본 키로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94084-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="94084-127">마우스 오른쪽 단추로 `Movie` 테이블 **> 데이터 보기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-127">Right click on the `Movie` table **> View Data**</span></span>

  ![동영상 테이블에서 열린 바로 가기 메뉴](working-with-sql/_static/ssox2.png)

  ![테이블 데이터를 보여 주는 열린 동영상 테이블](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="94084-130">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94084-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="94084-131">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="94084-131">Seed the database</span></span>

<span data-ttu-id="94084-132">*Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94084-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="94084-133">생성된 코드를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94084-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="94084-134">DB에 동영상이 있는 경우 시드 이니셜라이저가 반환되고 동영상이 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94084-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="94084-135">시드 이니셜라이저 추가</span><span class="sxs-lookup"><span data-stu-id="94084-135">Add the seed initializer</span></span>

<span data-ttu-id="94084-136">*Program.cs*의 내용을 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94084-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="94084-137">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="94084-137">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94084-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94084-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94084-139">DB의 모든 레코드를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-139">Delete all the records in the DB.</span></span> <span data-ttu-id="94084-140">브라우저 또는 SSOX에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94084-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="94084-141">시드 메서드가 실행되도록 앱에서 초기화를 적용하도록 합니다(`Startup` 클래스에서 메서드 호출).</span><span class="sxs-lookup"><span data-stu-id="94084-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="94084-142">초기화를 적용하려면 IIS Express를 중지하고 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="94084-143">다음 접근 방식 중 하나를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94084-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="94084-144">알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **종료** 또는 **사이트 중지**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="94084-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express 시스템 트레이 아이콘](working-with-sql/_static/iisExIcon.png)

    ![바로 가기 메뉴](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="94084-147">비디버그 모드에서 VS를 실행했던 경우 F5 키를 눌러 디버그 모드에서 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="94084-148">디버그 모드에서 VS를 실행했던 경우 디버거를 중지하고 F5 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="94084-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="94084-149">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94084-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="94084-150">DB의 모든 레코드 삭제(시드 메서드 실행을 위해).</span><span class="sxs-lookup"><span data-stu-id="94084-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="94084-151">앱을 중지 및 시작하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="94084-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="94084-152">앱에서 시드된 데이터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="94084-152">The app shows the seeded data.</span></span>

![동영상 데이터를 표시하는 Microsoft Edge에서 열린 MVC 동영상 애플리케이션](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="94084-154">[이전](adding-model.md)
> [다음](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="94084-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
