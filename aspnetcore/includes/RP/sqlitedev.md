### <a name="use-sqlite-for-development-sql-server-for-production"></a>개발에 SQLite를 사용하고 프로덕션에 SQL Server를 사용

SQLite를 선택하면 개발용 템플릿 생성 코드가 준비됩니다. 다음 코드에서는 시작에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 주입하는 방법을 보여줍니다. `IWebHostEnvironment`는 `ConfigureServices`가 SQLite를 사용하고 프로덕션에서 SQL Server를 사용할 수 있도록 주입됩니다.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
