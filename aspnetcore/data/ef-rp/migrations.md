---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 마이그레이션 - 4/8
author: rick-anderson
description: 이 자습서에서는 ASP.NET Core MVC 앱에서 데이터 모델 변경 관리를 위해 EF Core 마이그레이션 기능을 사용하는 것을 시작합니다.
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/migrations
ms.openlocfilehash: 406b94d74a99dcec3b2ce01a5eb3313c48819148
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517010"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="f7312-103">ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 마이그레이션 - 4/8</span><span class="sxs-lookup"><span data-stu-id="f7312-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="f7312-104">작성자: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7312-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="f7312-105">이 자습서에서는 데이터 모델 변경 관리를 위한 EF Core 마이그레이션 기능이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-105">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="f7312-106">해결할 수 없는 문제가 발생한 경우 [완성된 앱](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-106">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="f7312-107">새 앱이 개발되면 데이터 모델이 자주 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-107">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="f7312-108">모델이 변경될 때마다 모델이 데이터베이스와 동기화 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-108">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="f7312-109">이 자습서는 존재하지 않는 경우 데이터베이스를 만들도록 Entity Framework를 구성하여 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-109">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="f7312-110">데이터 모델이 변경될 때마다 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-110">Each time the data model changes:</span></span>

* <span data-ttu-id="f7312-111">DB가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-111">The DB is dropped.</span></span>
* <span data-ttu-id="f7312-112">EF는 모델과 일치하는 새 항목을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-112">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="f7312-113">앱은 테스트 데이터로 DB를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-113">The app seeds the DB with test data.</span></span>

<span data-ttu-id="f7312-114">DB를 데이터 모델과 동기화된 상태로 유지하는 이 접근 방식은 앱을 프로덕션 환경에 배포할 때까지 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-114">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="f7312-115">앱이 프로덕션 환경에서 실행 중인 경우 일반적으로 유지 관리해야 하는 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-115">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="f7312-116">앱은 변경(예: 새 열 추가)될 때마다 테스트 DB로 시작될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-116">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="f7312-117">EF Core 마이그레이션 기능은 EF Core에서 새 DB를 만드는 대신 DB 스키마를 업데이트하도록 하여 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-117">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="f7312-118">데이터 모델이 변경될 때 DB를 삭제하고 다시 작성하는 대신 마이그레이션은 스키마를 업데이트하고 기존 데이터를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-118">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="f7312-119">데이터베이스 삭제</span><span class="sxs-lookup"><span data-stu-id="f7312-119">Drop the database</span></span>

<span data-ttu-id="f7312-120">SSOX(**SQL Server 개체 탐색기**) 또는 `database drop` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-120">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f7312-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7312-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f7312-122">PMC(**패키지 관리자 콘솔**)에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-122">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="f7312-123">PMC에서 `Get-Help about_EntityFrameworkCore`를 실행하여 도움말 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-123">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f7312-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f7312-124">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f7312-125">명령 창을 열고 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-125">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="f7312-126">프로젝트 폴더에는 *Startup.cs* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-126">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="f7312-127">명령 창에서 다음을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-127">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="f7312-128">초기 마이그레이션 만들기 및 DB 업데이트</span><span class="sxs-lookup"><span data-stu-id="f7312-128">Create an initial migration and update the DB</span></span>

<span data-ttu-id="f7312-129">프로젝트를 빌드하고 첫 번째 마이그레이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-129">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f7312-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7312-130">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f7312-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f7312-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="f7312-132">Up 및 Down 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="f7312-132">Examine the Up and Down methods</span></span>

<span data-ttu-id="f7312-133">EF Core `migrations add` 명령은 DB를 생성하는 코드를 생성했습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-133">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="f7312-134">이 마이그레이션 코드는 *마이그레이션\<timestamp>_InitialCreate.cs* 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-134">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="f7312-135">`InitialCreate` 클래스dml `Up` 메서드는 데이터 모델 엔터티 집합에 해당하는 DB 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-135">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="f7312-136">`Down` 메서드는 다음 예제처럼 테이블을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-136">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="f7312-137">마이그레이션에서는 마이그레이션을 위한 데이터 모델 변경을 구현하기 위해 `Up` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-137">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="f7312-138">업데이트를 롤백하는 명령을 입력하면 마이그레이션에서 `Down` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-138">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="f7312-139">위의 코드는 초기 마이그레이션에 대한 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-139">The preceding code is for the initial migration.</span></span> <span data-ttu-id="f7312-140">이 코드는 `migrations add InitialCreate` 명령을 실행할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-140">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="f7312-141">파일 이름에는 마이그레이션 이름 매개 변수(이 예제에서 "InitialCreate")가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-141">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="f7312-142">마이그레이션 이름은 임의의 유효한 파일 이름이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-142">The migration name can be any valid file name.</span></span> <span data-ttu-id="f7312-143">마이그레이션에서 수행 중인 작업을 요약한 단어 또는 구를 선택하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-143">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="f7312-144">예를 들어 부서 테이블을 추가한 마이그레이션은 "AddDepartmentTable"이라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-144">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="f7312-145">초기 마이그레이션이 생성되었고 DB가 존재하는 경우:</span><span class="sxs-lookup"><span data-stu-id="f7312-145">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="f7312-146">DB 만들기 코드가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-146">The DB creation code is generated.</span></span>
* <span data-ttu-id="f7312-147">DB는 데이터 모델과 이미 일치하므로 DB 만들기 코드를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-147">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="f7312-148">DB 만들기 코드가 실행되면 DB가 데이터 모델과 이미 일치하므로 아무것도 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-148">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="f7312-149">앱이 새 환경에 배포되면 DB 만들기 코드를 실행하여 DB를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-149">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="f7312-150">이전에 DB는 삭제되었거나 존재하지 않으므로 마이그레이션에서 새 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-150">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="f7312-151">데이터 모델 스냅숏</span><span class="sxs-lookup"><span data-stu-id="f7312-151">The data model snapshot</span></span>

<span data-ttu-id="f7312-152">마이그레이션은 *Migrations/SchoolContextModelSnapshot.cs*에 현재 데이터베이스 스키마의 *스냅숏*을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-152">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="f7312-153">마이그레이션을 추가하면 EF가 데이터 모델을 스냅숏 파일과 비교하여 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-153">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="f7312-154">마이그레이션을 삭제하려면 다음 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-154">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f7312-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7312-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f7312-156">마이그레이션 제거</span><span class="sxs-lookup"><span data-stu-id="f7312-156">Remove-Migration</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f7312-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f7312-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations remove
```

<span data-ttu-id="f7312-158">자세한 내용은 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7312-158">For more information, see  [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="f7312-159">마이그레이션 제거 명령은 마이그레이션을 삭제하고 스냅숏이 올바르게 다시 설정되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-159">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="f7312-160">EnsureCreated 제거 및 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="f7312-160">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="f7312-161">초기 개발에는 `EnsureCreated`가 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-161">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="f7312-162">이 자습서에서는 마이그레이션이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-162">In this tutorial, migrations are used.</span></span> <span data-ttu-id="f7312-163">`EnsureCreated`에는 다음과 같은 제한 사항이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-163">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="f7312-164">마이그레이션을 무시하고 DB 및 스키마를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-164">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="f7312-165">마이그레이션 테이블을 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-165">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="f7312-166">마이그레이션에 사용할 수 *없습니다*.</span><span class="sxs-lookup"><span data-stu-id="f7312-166">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="f7312-167">DB를 삭제하고 자주 다시 생성하는 테스트 또는 신속한 프로토타입 만들기를 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-167">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="f7312-168">`EnsureCreated`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-168">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="f7312-169">앱을 실행하고 DB이 시드되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-169">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="f7312-170">데이터베이스 검사</span><span class="sxs-lookup"><span data-stu-id="f7312-170">Inspect the database</span></span>

<span data-ttu-id="f7312-171">**SQL Server 개체 탐색기**를 사용하여 DB를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-171">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="f7312-172">`__EFMigrationsHistory` 테이블이 추가된 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-172">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="f7312-173">`__EFMigrationsHistory` 테이블은 DB에 적용된 마이그레이션을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-173">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="f7312-174">`__EFMigrationsHistory` 테이블의 데이터를 보면 첫 번째 마이그레이션에 대해 한 행이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-174">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="f7312-175">앞의 CLI 출력 예제의 마지막 로그는 이 행을 만드는 INSERT 문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-175">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="f7312-176">앱을 실행하고 모든 항목이 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-176">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="f7312-177">프로덕션 환경에서 마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="f7312-177">Applying migrations in production</span></span>

<span data-ttu-id="f7312-178">프로덕션 앱은 애플리케이션 시작 시 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)를 호출하지 **않는 것이** 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-178">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="f7312-179">서버 팜의 앱에서 `Migrate`를 호출하면 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-179">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="f7312-180">예를 들어, 앱이 스케일 아웃으로 클라우드에 배포된 경우(앱의 여러 인스턴스가 실행 중임)</span><span class="sxs-lookup"><span data-stu-id="f7312-180">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="f7312-181">데이터베이스 마이그레이션은 배포의 일부로 제어된 방식으로 수행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-181">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="f7312-182">프로덕션 데이터베이스 마이그레이션 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-182">Production database migration approaches include:</span></span>

* <span data-ttu-id="f7312-183">마이그레이션을 사용하여 SQL 스크립트를 작성하고 배포 시 SQL 스크립트 사용</span><span class="sxs-lookup"><span data-stu-id="f7312-183">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="f7312-184">제어된 환경에서 `dotnet ef database update` 실행</span><span class="sxs-lookup"><span data-stu-id="f7312-184">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="f7312-185">EF Core는 `__MigrationsHistory` 테이블을 사용하여 마이그레이션을 실행해야 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-185">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="f7312-186">DB가 최신 상태이면 마이그레이션이 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-186">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f7312-187">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f7312-187">Troubleshooting</span></span>

<span data-ttu-id="f7312-188">[완료된 앱](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-188">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="f7312-189">앱에서는 다음과 같은 예외가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7312-189">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="f7312-190">해결책: `dotnet ef database update` 실행</span><span class="sxs-lookup"><span data-stu-id="f7312-190">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="f7312-191">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f7312-191">Additional resources</span></span>

* [<span data-ttu-id="f7312-192">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="f7312-192">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="f7312-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)</span><span class="sxs-lookup"><span data-stu-id="f7312-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="f7312-194">패키지 관리자 콘솔(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="f7312-194">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="f7312-195">[이전](xref:data/ef-rp/sort-filter-page)
> [다음](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="f7312-195">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>
