Id 스 캐 폴더를 실행 합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **추가** > **스 캐 폴드 된 새 항목**합니다.
* 왼쪽된 창에서 합니다 **스 캐 폴드 추가** 대화 상자에서 **Identity** > **추가**합니다.
* **Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.
  * 기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다. 예를 `~/Pages/Shared/_Layout.cshtml` 들어 MVC `~/Views/Shared/_Layout.cshtml` 프로젝트에 대 한 Razor Pages
  * 선택 된 **+** 새 단추 **데이터 컨텍스트 클래스**합니다.
* 선택 **추가**합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

프로젝트 (\*.csproj) 파일에 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 추가 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

프로젝트 폴더에서 원하는 옵션을 사용 하 여 Id 스 캐 폴더를 실행 합니다. 예를 들어 기본 UI와 최소 파일 수를 사용 하 여 id를 설정 하려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
