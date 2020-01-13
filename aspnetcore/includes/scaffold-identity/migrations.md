생성된 Identity 데이터베이스 코드는 [Entity Framework Core 마이그레이션](/ef/core/managing-schemas/migrations/)을 필요로 합니다. 마이그레이션을 만들고 데이터베이스를 업데이트 합니다. 예를 들어 다음 명령을 실행합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual studio **패키지 관리자 콘솔**에서:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

`Add-Migration` 명령의 "CreateIdentitySchema" name 매개 변수는 임의적입니다. `"CreateIdentitySchema"`는 마이그레이션에 대해 설명합니다.
