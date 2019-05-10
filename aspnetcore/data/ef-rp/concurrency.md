---
title: ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 동시성 - 8/8
author: rick-anderson
description: 이 자습서에는 여러 사용자가 동시에 같은 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2018
uid: data/ef-rp/concurrency
ms.openlocfilehash: 17ce0c111daabe2c7bbf4795b658856568c85158
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886178"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="2ecda-103">ASP.NET Core에서 EF Core를 사용한 Razor 페이지 - 동시성 - 8/8</span><span class="sxs-lookup"><span data-stu-id="2ecda-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="2ecda-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) 및 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="2ecda-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="2ecda-105">이 자습서에는 여러 사용자가 동시에(같은 시간에) 엔터티를 업데이트하는 경우 충돌을 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="2ecda-106">해결할 수 없는 문제가 발생할 경우 [완성된 앱을 다운로드하거나 봅니다](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="2ecda-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="2ecda-107">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="2ecda-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="2ecda-108">동시성 충돌</span><span class="sxs-lookup"><span data-stu-id="2ecda-108">Concurrency conflicts</span></span>

<span data-ttu-id="2ecda-109">동시성 충돌이 발생한 경우:</span><span class="sxs-lookup"><span data-stu-id="2ecda-109">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="2ecda-110">사용자는 엔터티에 대한 편집 페이지를 탐색합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-110">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="2ecda-111">첫 번째 사용자가 DB에 변경 내용을 기록하기 전에 다른 사용자가 동일한 엔터티를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-111">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="2ecda-112">동시성 감지가 활성화되지 않으면 동시 업데이트 시 다음이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-112">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="2ecda-113">마지막 업데이트가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-113">The last update wins.</span></span> <span data-ttu-id="2ecda-114">즉, 마지막 업데이트 값이 DB에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-114">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="2ecda-115">현재 업데이트 중 첫 번째 작업이 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-115">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="2ecda-116">낙관적 동시성</span><span class="sxs-lookup"><span data-stu-id="2ecda-116">Optimistic concurrency</span></span>

<span data-ttu-id="2ecda-117">낙관적 동시성은 동시성 충돌 발생을 허용하고, 이에 적절하게 반응합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-117">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="2ecda-118">예를 들어, Jane이 부서 편집 페이지를 방문하여 영어 부서 예산을 $350,000.00에서 $0.00으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-118">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![예산을 0으로 변경](concurrency/_static/change-budget.png)

<span data-ttu-id="2ecda-120">Jane이 **저장**을 클릭하기 전에, John이 동일한 페이지를 방문하여 시작 날짜 필드를 2007년 9월 1일에서 2013년 9월 1일로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-120">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![시작 날짜를 2013으로 변경](concurrency/_static/change-date.png)

<span data-ttu-id="2ecda-122">Jane이 먼저 **저장**을 클릭하여 브라우저에 인덱스 페이지가 표시될 때 변경 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-122">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![0으로 변경된 예산](concurrency/_static/budget-zero.png)

<span data-ttu-id="2ecda-124">John이 예산이 여전히 $350,000.00인 편집 페이지에서 **저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-124">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="2ecda-125">다음 작업은 동시성 충돌을 처리하는 방법에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-125">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="2ecda-126">낙관적 동시성에는 다음과 같은 옵션이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-126">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="2ecda-127">사용자가 수정한 속성을 추적하고 DB에서 해당하는 열만 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-127">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="2ecda-128">이 시나리오에서는 데이터가 손실되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-128">In the scenario, no data would be lost.</span></span> <span data-ttu-id="2ecda-129">다른 속성이 두 사용자에 의해 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-129">Different properties were updated by the two users.</span></span> <span data-ttu-id="2ecda-130">다음에 누군가가 영어 부서를 찾아볼 때는 Jane과 John의 변경 내용을 모두 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-130">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="2ecda-131">이 업데이트 메서드는 데이터 손실로 이어질 수 있는 충돌 횟수를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-131">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="2ecda-132">이 방법은:</span><span class="sxs-lookup"><span data-stu-id="2ecda-132">This approach:</span></span>
 
  * <span data-ttu-id="2ecda-133">같은 속성에 변경 사항이 적용된 경우 데이터 손실을 방지할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-133">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="2ecda-134">일반적으로 웹앱에서는 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-134">Is generally not practical in a web app.</span></span> <span data-ttu-id="2ecda-135">페치된 값과 새 값을 모두 추적하기 위해 유효한 상태를 유지해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-135">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="2ecda-136">많은 양의 상태를 유지하는 것은 응용 프로그램 성능에 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-136">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="2ecda-137">엔터티에 대한 동시성 감지보다 앱 복잡성이 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-137">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="2ecda-138">Jane의 변경 사항을 John의 변경 사항으로 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-138">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="2ecda-139">다음에 누군가가 영어 부서를 찾아볼 때 2013년 9월 1일과 페치된 $350,000.00 값을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-139">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="2ecda-140">이 방법을 *클라이언트 우선* 또는 *최종 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-140">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="2ecda-141">(클라이언트의 모든 값은 데이터 저장소에 포함된 값에 우선합니다.) 동시성 처리에 대한 코딩을 수행하지 않은 경우 클라이언트 우선이 자동으로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-141">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="2ecda-142">John의 변경 내용이 DB에서 업데이트되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-142">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="2ecda-143">일반적으로 앱은:</span><span class="sxs-lookup"><span data-stu-id="2ecda-143">Typically, the app would:</span></span>

  * <span data-ttu-id="2ecda-144">오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-144">Display an error message.</span></span>
  * <span data-ttu-id="2ecda-145">데이터의 현재 상태를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-145">Show the current state of the data.</span></span>
  * <span data-ttu-id="2ecda-146">사용자가 변경 내용을 다시 적용하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-146">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="2ecda-147">이를 *저장소 우선* 시나리오라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-147">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="2ecda-148">(데이터 저장소 값은 클라이언트가 전송한 값에 우선합니다.) 이 자습서에서는 저장소 우선 시나리오를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-148">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="2ecda-149">이 메서드는 사용자 알림 없이 덮어쓴 변경 내용이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-149">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="2ecda-150">동시성 처리</span><span class="sxs-lookup"><span data-stu-id="2ecda-150">Handling concurrency</span></span> 

<span data-ttu-id="2ecda-151">속성이 [동시성 토큰](/ef/core/modeling/concurrency)으로 구성되는 경우:</span><span class="sxs-lookup"><span data-stu-id="2ecda-151">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="2ecda-152">EF Core는 속성이 페치된 후 수정되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-152">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="2ecda-153">확인 작업은 [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) 또는 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_)가 호출될 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-153">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="2ecda-154">속성이 페치된 후 변경된 경우 [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0)이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-154">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="2ecda-155">`DbUpdateConcurrencyException`의 throw를 지원하도록 DB 및 데이터 모델을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-155">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="2ecda-156">속성에서 동시성 충돌 감지</span><span class="sxs-lookup"><span data-stu-id="2ecda-156">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="2ecda-157">동시성 충돌은 [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) 특성을 사용하여 속성 수준에서 감지될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-157">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="2ecda-158">특성은 모델에서 여러 속성에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-158">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="2ecda-159">자세한 내용은 [데이터 주석 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ecda-159">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="2ecda-160">`[ConcurrencyCheck]` 특성은 이 자습서에서 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-160">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="2ecda-161">행에서 동시성 충돌 감지</span><span class="sxs-lookup"><span data-stu-id="2ecda-161">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="2ecda-162">동시성 충돌을 감지하기 위해 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 추적 열이 모델에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-162">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="2ecda-163">`rowversion`은:</span><span class="sxs-lookup"><span data-stu-id="2ecda-163">`rowversion` :</span></span>

* <span data-ttu-id="2ecda-164">SQL Server 한정적입니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-164">Is SQL Server specific.</span></span> <span data-ttu-id="2ecda-165">다른 데이터베이스는 유사한 기능을 제공하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-165">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="2ecda-166">DB에서 페치된 이후로 엔터티가 변경되지 않았는지를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-166">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="2ecda-167">DB는 행이 업데이트될 때마다 증가되는 순차적 `rowversion` 번호를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-167">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="2ecda-168">`Update` 또는 `Delete` 명령에서 `Where` 절은 `rowversion`의 페치된 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-168">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="2ecda-169">업데이트되는 행이 변경되는 경우:</span><span class="sxs-lookup"><span data-stu-id="2ecda-169">If the row being updated has changed:</span></span>

* <span data-ttu-id="2ecda-170">`rowversion`은 페치된 값에 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-170">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="2ecda-171">`Update` 또는 `Delete` 명령은 `Where` 절이 페치된 `rowversion`을 포함하므로 행을 찾지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-171">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="2ecda-172">`DbUpdateConcurrencyException`이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-172">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="2ecda-173">EF Core에서 `Update` 또는 `Delete` 명령에 의해 업데이트된 행이 없는 경우 동시성 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-173">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="2ecda-174">추적 속성을 부서 엔터티에 추가</span><span class="sxs-lookup"><span data-stu-id="2ecda-174">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="2ecda-175">*Models/Department.cs*에서 RowVersion으로 명명된 추적 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-175">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="2ecda-176">[타임스탬프](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 특성은 이 열이 `Update` 및 `Delete` 명령의 `Where` 절에 포함되어 있음을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-176">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="2ecda-177">SQL `rowversion` 형식이 대체하기 전에 이전 버전의 SQL Server가 SQL `timestamp` 데이터 형식을 사용했으므로 특성은 `Timestamp`라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-177">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="2ecda-178">또한 흐름 API가 추적 속성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-178">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="2ecda-179">다음 코드는 부서 이름이 업데이트될 때 EF Core에서 생성된 T-SQL의 일부를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-179">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

<span data-ttu-id="2ecda-180">위에 강조 표시된 코드는 `RowVersion`을 포함하는 `WHERE` 절을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-180">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="2ecda-181">DB `RowVersion`이 `RowVersion` 매개 변수(`@p2`)와 다를 경우 행은 업데이트되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-181">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="2ecda-182">다음 강조 표시된 코드는 정확히 한 개의 행이 업데이트되었음을 확인하는 T-SQL을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-182">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

<span data-ttu-id="2ecda-183">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql)는 마지막 명령문의 영향을 받는 행 수를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-183">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="2ecda-184">행이 업데이트되지 않은 경우 EF Core는 `DbUpdateConcurrencyException`을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-184">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="2ecda-185">Visual Studio의 출력 창에서 T-SQL EF Core 생성을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-185">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="2ecda-186">DB 업데이트</span><span class="sxs-lookup"><span data-stu-id="2ecda-186">Update the DB</span></span>

<span data-ttu-id="2ecda-187">`RowVersion` 속성을 추가하면 마이그레이션이 필요한 DB 모델이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-187">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="2ecda-188">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-188">Build the project.</span></span> <span data-ttu-id="2ecda-189">명령 창에서 다음을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-189">Enter the following in a command window:</span></span>

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="2ecda-190">이전 명령은:</span><span class="sxs-lookup"><span data-stu-id="2ecda-190">The preceding commands:</span></span>

* <span data-ttu-id="2ecda-191">*Migrations/{time stamp}_RowVersion.cs* 마이그레이션 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-191">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="2ecda-192">*Migrations/SchoolContextModelSnapshot.cs* 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-192">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="2ecda-193">업데이트는 다음 강조 표시된 코드를 `BuildModel` 메서드에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-193">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* <span data-ttu-id="2ecda-194">마이그레이션을 실행하여 DB를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-194">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="2ecda-195">부서 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="2ecda-195">Scaffold the Departments model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2ecda-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ecda-196">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="2ecda-197">[학생 모델 스캐폴드](xref:data/ef-rp/intro#scaffold-the-student-model)의 지침을 따르고 `Department`를 모델 클래스로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-197">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Department` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2ecda-198">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2ecda-198">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="2ecda-199">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-199">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="2ecda-200">위의 명령은 `Department` 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-200">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="2ecda-201">Visual Studio에서 프로젝트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-201">Open the project in Visual Studio.</span></span>

<span data-ttu-id="2ecda-202">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-202">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="2ecda-203">부서 인덱스 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="2ecda-203">Update the Departments Index page</span></span>

<span data-ttu-id="2ecda-204">스캐폴딩 엔진은 인덱스 페이지에 대한 `RowVersion` 열을 만들지만, 해당 필드는 표시되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-204">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="2ecda-205">이 자습서에서는 동시성을 이해하는 데 도움을 주기 위해 `RowVersion`의 마지막 바이트가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-205">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="2ecda-206">마지막 바이트는 고유하게 보장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-206">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="2ecda-207">실제 앱에서는 `RowVersion` 또는 `RowVersion`의 마지막 바이트가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-207">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="2ecda-208">인덱스 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-208">Update the Index page:</span></span>

* <span data-ttu-id="2ecda-209">인덱스를 부서로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-209">Replace Index with Departments.</span></span>
* <span data-ttu-id="2ecda-210">`RowVersion`을 포함하는 표시를 `RowVersion`의 마지막 바이트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-210">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="2ecda-211">FirstMidName을 FullName으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-211">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="2ecda-212">다음 표시는 업데이트된 페이지를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-212">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="2ecda-213">편집 페이지 모델 업데이트</span><span class="sxs-lookup"><span data-stu-id="2ecda-213">Update the Edit page model</span></span>

<span data-ttu-id="2ecda-214">*pages\departments\edit.cshtml.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-214">Update *pages\departments\edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="2ecda-215">동시성 문제를 감지하기 위해 [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)가 페치된 엔터티의 `rowVersion` 값으로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-215">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="2ecda-216">EF Core는 원본 `RowVersion` 값을 포함하는 WHERE 절과 함께 SQL UPDATE 명령을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-216">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="2ecda-217">UPDATE 명령의 영향을 받는 행이 없는 경우(행에 원래 `RowVersion` 값이 없음) `DbUpdateConcurrencyException` 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-217">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="2ecda-218">위의 코드에서 `Department.RowVersion`은 엔터티가 페치될 때 값입니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-218">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="2ecda-219">`OriginalValue`는 `FirstOrDefaultAsync`가 이 메서드에서 호출될 때 DB의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-219">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="2ecda-220">다음 코드는 클라이언트 값(이 메서드에 게시된 값) 및 DB 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-220">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="2ecda-221">다음 코드는 DB 값이 `OnPostAsync`에 게시된 값과 다른 각 열에 대한 사용자 오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-221">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="2ecda-222">다음 강조 표시된 코드는 `RowVersion` 값을 DB에서 검색된 새 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-222">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="2ecda-223">다음에 사용자가 **저장**을 클릭하면, 편집 페이지의 마지막 표시 이후 발생한 동시성 오류만 catch됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-223">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="2ecda-224">`ModelState`에 이전 `RowVersion` 값이 있으므로 `ModelState.Remove` 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-224">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="2ecda-225">Razor 페이지에서 필드에 대한 `ModelState` 값은 모델 속성 값에 우선합니다(모두 있는 경우).</span><span class="sxs-lookup"><span data-stu-id="2ecda-225">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="2ecda-226">편집 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="2ecda-226">Update the Edit page</span></span>

<span data-ttu-id="2ecda-227">*Pages/Departments/Edit.cshtml*을 다음 표시로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-227">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="2ecda-228">위의 표시는:</span><span class="sxs-lookup"><span data-stu-id="2ecda-228">The preceding markup:</span></span>

* <span data-ttu-id="2ecda-229">`page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-229">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="2ecda-230">숨겨진 행 버전을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-230">Adds a hidden row version.</span></span> <span data-ttu-id="2ecda-231">포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-231">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="2ecda-232">디버깅을 위해 `RowVersion`의 마지막 바이트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-232">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="2ecda-233">`ViewData`를 강력한 형식의 `InstructorNameSL`로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-233">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="2ecda-234">편집 페이지로 동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="2ecda-234">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="2ecda-235">영어 부서에 있는 편집의 두 브라우저 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-235">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="2ecda-236">앱을 실행하고 부서를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-236">Run the app and select Departments.</span></span>
* <span data-ttu-id="2ecda-237">영어 부서에 대한 **편집** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-237">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="2ecda-238">첫 번째 탭에서 영어 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-238">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="2ecda-239">두 개의 브라우저 탭에 동일한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-239">The two browser tabs display the same information.</span></span>

<span data-ttu-id="2ecda-240">첫 번째 브라우저 탭의 이름을 변경하고 **저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-240">Change the name in the first browser tab and click **Save**.</span></span>

![변경 후 부서 편집 페이지 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="2ecda-242">브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-242">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="2ecda-243">업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-243">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="2ecda-244">두 번째 브라우저 탭에서 다른 필드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-244">Change a different field in the second browser tab.</span></span>

![변경 후 부서 편집 페이지 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="2ecda-246">**저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-246">Click **Save**.</span></span> <span data-ttu-id="2ecda-247">DB 값과 일치하지 않는 모든 필드에 대한 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-247">You see error messages for all fields that don't match the DB values:</span></span>

![부서 편집 페이지 오류 메시지](concurrency/_static/edit-error.png)

<span data-ttu-id="2ecda-249">이 브라우저 창은 Name 필드 변경용으로 의도되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-249">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="2ecda-250">현재 값(Languages)을 복사하여 Name 필드에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-250">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="2ecda-251">필드 밖을 탭합니다. 클라이언트 쪽 유효성 검사가 오류 메시지를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-251">Tab out. Client-side validation removes the error message.</span></span>

![부서 편집 페이지 오류 메시지](concurrency/_static/cv.png)

<span data-ttu-id="2ecda-253">**저장**을 다시 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-253">Click **Save** again.</span></span> <span data-ttu-id="2ecda-254">두 번째 브라우저 탭에 입력한 값이 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-254">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="2ecda-255">인덱스 페이지에 저장된 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-255">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="2ecda-256">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="2ecda-256">Update the Delete page</span></span>

<span data-ttu-id="2ecda-257">다음 코드로 삭제 페이지 모델을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-257">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="2ecda-258">페이지 삭제는 엔터티가 페치된 후 변경될 때 동시성 충돌을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-258">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="2ecda-259">`Department.RowVersion`은 엔터티가 페치될 때 행 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-259">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="2ecda-260">EF Core가 SQL DELETE 명령을 만들 때 `RowVersion`과 함께 WHERE 절이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-260">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="2ecda-261">SQL DELETE 명령의 영향을 받는 행이 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="2ecda-261">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="2ecda-262">SQL DELETE 명령의 `RowVersion`이 DB의 `RowVersion`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-262">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="2ecda-263">DbUpdateConcurrencyException 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-263">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="2ecda-264">`OnGetAsync`가 `concurrencyError`를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-264">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="2ecda-265">삭제 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="2ecda-265">Update the Delete page</span></span>

<span data-ttu-id="2ecda-266">*Pages/Departments/Delete.cshtml*을 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-266">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="2ecda-267">위의 표시는 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-267">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="2ecda-268">`page` 지시어를 `@page`에서 `@page "{id:int}"`로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-268">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="2ecda-269">오류 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-269">Adds an error message.</span></span>
* <span data-ttu-id="2ecda-270">**Administrator** 필드의 FirstMidName을 FullName으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-270">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="2ecda-271">마지막 바이트를 표시하도록 `RowVersion`을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-271">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="2ecda-272">숨겨진 행 버전을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-272">Adds a hidden row version.</span></span> <span data-ttu-id="2ecda-273">포스트백은 값을 바인딩하므로 `RowVersion`을 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-273">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="2ecda-274">삭제 페이지로 동시성 충돌 테스트</span><span class="sxs-lookup"><span data-stu-id="2ecda-274">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="2ecda-275">테스트 부서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-275">Create a test department.</span></span>

<span data-ttu-id="2ecda-276">테스트 부서에 있는 삭제의 두 브라우저 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-276">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="2ecda-277">앱을 실행하고 부서를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-277">Run the app and select Departments.</span></span>
* <span data-ttu-id="2ecda-278">테스트 부서에 대한 **삭제** 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 **새 탭에서 열기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-278">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="2ecda-279">테스트 부서에 대한 **편집** 하이퍼링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-279">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="2ecda-280">두 개의 브라우저 탭에 동일한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-280">The two browser tabs display the same information.</span></span>

<span data-ttu-id="2ecda-281">첫 번째 브라우저 탭의 예산을 변경하고 **저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-281">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="2ecda-282">브라우저에 변경된 값과 업데이트된 rowVersion 표시기가 있는 인덱스 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-282">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="2ecda-283">업데이트된 rowVersion 표시기는 다른 탭의 두 번째 포스트백에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-283">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="2ecda-284">두 번째 탭에서 테스트 부서를 삭제합니다. 동시성 오류는 DB의 현재 값으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-284">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="2ecda-285">`RowVersion`이 업데이트되고 부서가 삭제되지 않는 한 **삭제**를 클릭하면 엔터티가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2ecda-285">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="2ecda-286">데이터 모델을 상속하는 방법은 [상속](xref:data/ef-mvc/inheritance)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2ecda-286">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="2ecda-287">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2ecda-287">Additional resources</span></span>

* [<span data-ttu-id="2ecda-288">EF Core의 동시성 토큰</span><span class="sxs-lookup"><span data-stu-id="2ecda-288">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="2ecda-289">EF Core의 동시성 처리</span><span class="sxs-lookup"><span data-stu-id="2ecda-289">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="2ecda-290">이 자습서의 YouTube 버전(동시성 충돌 처리)</span><span class="sxs-lookup"><span data-stu-id="2ecda-290">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="2ecda-291">이 자습서의 YouTube 버전(2부)</span><span class="sxs-lookup"><span data-stu-id="2ecda-291">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="2ecda-292">이 자습서의 YouTube 버전(3부)</span><span class="sxs-lookup"><span data-stu-id="2ecda-292">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="2ecda-293">이전</span><span class="sxs-lookup"><span data-stu-id="2ecda-293">Previous</span></span>](xref:data/ef-rp/update-related-data)
