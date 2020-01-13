Identity 스캐폴더를 실행합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 프로젝트 > **추가** > **스캐폴드 항목 새로 만들기**를 마우스 오른쪽 단추로 클릭합니다.
* **새 스캐폴드 항목 추가** 대화 상자의 왼쪽된 창에서 **ID** > **추가**를 선택합니다.
* **ID 추가** 대화 상자에서 원하는 옵션을 선택합니다.
  * 기존 레이아웃 페이지를 선택하세요. 그렇지 않으면 잘못된 태그로 사용하여 레이아웃 파일을 덮어쓰게 됩니다. 예를 들어 Razor Pages에 대한 `~/Views/Shared/_Layout.cshtml`이나 MVC 프로젝트에 대한 `~/Pages/Shared/_Layout.cshtml`을 선택합니다.
  * **+** 단추를 선택해서 새로운 **데이터 컨텍스트 클래스**를 생성합니다.
* **추가**를 선택합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core 스캐폴더를 이전에 설치하지 않은 경우 지금 설치합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

프로젝트(\*.csproj) 파일에 필요한 NuGet 패키지 참조를 추가합니다. 프로젝트 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Identity 스캐폴더 옵션을 나열하려면 다음 명령을 실행합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

프로젝트 폴더에서 원하는 옵션을 사용하여 Identity 스캐폴더를 실행합니다. 예를 들어 기본 UI와 최소 파일 수를 사용하여 Identity를 설정하려면 다음 명령을 실행합니다.

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
