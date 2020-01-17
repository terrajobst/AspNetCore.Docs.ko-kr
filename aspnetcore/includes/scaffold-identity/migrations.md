<span data-ttu-id="83bb7-101">생성된 Identity 데이터베이스 코드는 [Entity Framework Core 마이그레이션](/ef/core/managing-schemas/migrations/)을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="83bb7-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="83bb7-102">마이그레이션을 만들고 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="83bb7-102">Create a migration and update the database.</span></span> <span data-ttu-id="83bb7-103">예를 들어 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="83bb7-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83bb7-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83bb7-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83bb7-105">Visual studio **패키지 관리자 콘솔**에서:</span><span class="sxs-lookup"><span data-stu-id="83bb7-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="83bb7-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="83bb7-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="83bb7-107">`Add-Migration` 명령의 "CreateIdentitySchema" name 매개 변수는 임의적입니다.</span><span class="sxs-lookup"><span data-stu-id="83bb7-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="83bb7-108">`"CreateIdentitySchema"`는 마이그레이션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="83bb7-108">`"CreateIdentitySchema"` describes the migration.</span></span>
