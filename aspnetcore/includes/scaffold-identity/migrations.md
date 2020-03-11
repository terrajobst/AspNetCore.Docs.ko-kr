생성 된 Id 데이터베이스 코드에는 [Entity Framework Core 마이그레이션이](/ef/core/managing-schemas/migrations/)필요 합니다. 마이그레이션을 만들고 데이터베이스를 업데이트 합니다. 예를 들어 다음 명령을 실행합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio **패키지 관리자 콘솔**에서 다음을 수행 합니다.

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

`Add-Migration` 명령에 대 한 "CreateIdentitySchema" name 매개 변수는 임의입니다. `"CreateIdentitySchema"` 마이그레이션에 대해 설명 합니다.
