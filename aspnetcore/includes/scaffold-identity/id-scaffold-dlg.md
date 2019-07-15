<span data-ttu-id="882f0-101">Identity 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="882f0-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="882f0-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="882f0-103">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **추가** > **스 캐 폴드 된 새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="882f0-104">왼쪽된 창에서 합니다 **스 캐 폴드 추가** 대화 상자에서 **Identity** > **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="882f0-105">에 **ADD Id** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="882f0-106">기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어쓰게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="882f0-107">예를 들어 `~/Pages/Shared/_Layout.cshtml` Razor 페이지에 대 한 `~/Views/Shared/_Layout.cshtml` MVC 프로젝트</span><span class="sxs-lookup"><span data-stu-id="882f0-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="882f0-108">선택 된 **+** 새 단추 **데이터 컨텍스트 클래스**합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="882f0-109">선택 **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="882f0-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="882f0-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="882f0-111">ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="882f0-112">에 대 한 패키지 참조 추가 [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 프로젝트 (\*.csproj) 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-112">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="882f0-113">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-113">Run the following command in the project directory:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="882f0-114">Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-114">Run the following command to list the Identity scaffolder options:</span></span>

```cli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="882f0-115">프로젝트 폴더에서 원하는 옵션을 사용 하 여 Identity 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="882f0-116">예를 들어, 기본 UI 사용 하 여 id 및 파일의 최소 수를을 설정 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="882f0-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```cli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
