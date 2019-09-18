<span data-ttu-id="179de-101">Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="179de-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="179de-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="179de-103">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **추가** > **스 캐 폴드 된 새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="179de-104">**스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 **id** > **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="179de-105">**Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="179de-106">기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="179de-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="179de-107">*기존\_레이아웃* 을 선택한 경우에는이 파일을 덮어쓰지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="179de-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="179de-108">예: `~/Pages/Shared/_Layout.cshtml` MVC 프로젝트에 `~/Views/Shared/_Layout.cshtml` 대 한 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="179de-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="179de-109">기존 데이터 컨텍스트를 사용 하려면 재정의할 파일을 하나 이상 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="179de-110">데이터 컨텍스트를 추가 하려면 파일을 하나 이상 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="179de-111">데이터 컨텍스트 클래스를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-111">Select your data context class.</span></span>
  * <span data-ttu-id="179de-112">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-112">Select **Add**.</span></span>
* <span data-ttu-id="179de-113">새 사용자 컨텍스트를 만들고 Id에 대 한 사용자 지정 사용자 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="179de-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="179de-114">선택 된 **+** 새 단추 **데이터 컨텍스트 클래스**합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="179de-115">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-115">Select **Add**.</span></span>

<span data-ttu-id="179de-116">참고: 새 사용자 컨텍스트를 만드는 경우 재정의할 파일을 선택할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="179de-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="179de-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="179de-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="179de-118">ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="179de-119">프로젝트 (\*.csproj) 파일에 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="179de-120">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="179de-121">Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="179de-122">프로젝트 폴더에서 원하는 옵션을 사용 하 여 Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="179de-123">예를 들어 기본 UI를 사용 하 여 id를 설정 하 고 최소 파일 수를 설정 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="179de-124">DB 컨텍스트에 대 한 올바른 정규화 된 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="179de-125">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="179de-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="179de-126">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 파일 목록을 큰따옴표로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="179de-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="179de-127">예:</span><span class="sxs-lookup"><span data-stu-id="179de-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="179de-128">`--files` 플래그`--useDefaultUI` 또는 플래그를 지정 하지 않고 id 스 캐 폴더를 실행 하면 사용 가능한 모든 id UI 페이지가 프로젝트에 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="179de-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
