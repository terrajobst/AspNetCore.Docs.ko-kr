<span data-ttu-id="e2318-101">생성 된 Id 데이터베이스 코드에는 [Entity Framework Core 마이그레이션이](/ef/core/managing-schemas/migrations/)필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2318-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="e2318-102">마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2318-102">Create a migration and update the database.</span></span> <span data-ttu-id="e2318-103">예를 들어 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2318-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2318-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2318-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2318-105">Visual studio에서 **패키지 관리자 콘솔**:</span><span class="sxs-lookup"><span data-stu-id="e2318-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e2318-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e2318-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="e2318-107">`Add-Migration` 명령에 대 한 "CreateIdentitySchema" name 매개 변수는 임의입니다.</span><span class="sxs-lookup"><span data-stu-id="e2318-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="e2318-108">`"CreateIdentitySchema"` 마이그레이션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2318-108">`"CreateIdentitySchema"` describes the migration.</span></span>
