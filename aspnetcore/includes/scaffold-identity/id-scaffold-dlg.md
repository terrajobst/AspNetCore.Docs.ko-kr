<span data-ttu-id="ae91e-101">Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae91e-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae91e-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ae91e-103">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **추가** > **스 캐 폴드 된 새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ae91e-104">왼쪽된 창에서 합니다 **스 캐 폴드 추가** 대화 상자에서 **Identity** > **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="ae91e-105">**Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="ae91e-106">기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="ae91e-107">예를 들어 MVC 프로젝트에 대 한 Razor Pages `~/Views/Shared/_Layout.cshtml`에 대 한 `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="ae91e-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="ae91e-108">선택 된 **+** 새 단추 **데이터 컨텍스트 클래스**합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="ae91e-109">선택 **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ae91e-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ae91e-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ae91e-111">ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="ae91e-112">프로젝트 (\*.csproj) 파일에 필요한 NuGet 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-112">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="ae91e-113">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="ae91e-114">Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="ae91e-115">프로젝트 폴더에서 원하는 옵션을 사용 하 여 Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="ae91e-116">예를 들어 기본 UI와 최소 파일 수를 사용 하 여 id를 설정 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ae91e-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
