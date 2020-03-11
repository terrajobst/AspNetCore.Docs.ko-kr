::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0826a-101">Identity 스캐폴더를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0826a-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0826a-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0826a-103">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **새 스 캐 폴드 항목**> **추가** 를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0826a-104">**스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 **id** > **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="0826a-105">**Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="0826a-106">기존 레이아웃 페이지를 선택하세요. 그렇지 않으면 잘못된 태그로 레이아웃 파일을 덮어쓰게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="0826a-107">기존 *\_레이아웃* 을 선택한 경우에는이 파일을 덮어쓰지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="0826a-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="0826a-108">예: MVC 프로젝트에 대 한 Razor Pages `~/Views/Shared/_Layout.cshtml`에 대 한 `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="0826a-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="0826a-109">기존 데이터 컨텍스트를 사용하려면 재정의할 파일을 하나 이상 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="0826a-110">데이터 컨텍스트를 추가하려면 파일을 하나 이상 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="0826a-111">데이터 컨텍스트 클래스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-111">Select your data context class.</span></span>
  * <span data-ttu-id="0826a-112">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-112">Select **Add**.</span></span>
* <span data-ttu-id="0826a-113">새 사용자 컨텍스트를 만들고 Identity에 대한 사용자 지정 사용자 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="0826a-114">**+** 단추를 선택 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="0826a-115">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-115">Select **Add**.</span></span>

<span data-ttu-id="0826a-116">참고: 새 사용자 컨텍스트를 만드는 경우 재정의할 파일을 선택할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0826a-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0826a-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0826a-118">ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="0826a-119">프로젝트 (\*.csproj) 파일에 필요한 NuGet 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-119">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="0826a-120">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="0826a-121">Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="0826a-122">프로젝트 폴더에서 원하는 옵션을 사용하여 Identity 스캐폴더를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="0826a-123">예를 들어 기본 UI와 최소 파일 수를 사용하여 Identity를 설정하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="0826a-124">DB 컨텍스트에 대한 올바른 정규화된 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="0826a-125">PowerShell은 세미콜론을 명령 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="0826a-126">PowerShell을 사용하는 경우 파일 목록에서 세미콜론을 이스케이프하거나 파일 목록을 큰따옴표로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="0826a-127">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="0826a-128">`--files` 플래그 또는 `--useDefaultUI` 플래그를 지정 하지 않고 Id 스 캐 폴더를 실행 하면 사용 가능한 모든 Id UI 페이지가 프로젝트에 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0826a-129">Identity 스캐폴더를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-129">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0826a-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0826a-130">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0826a-131">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **새 스 캐 폴드 항목**> **추가** 를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-131">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0826a-132">**스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 **id** > **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-132">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="0826a-133">**Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-133">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="0826a-134">기존 레이아웃 페이지를 선택하세요. 그렇지 않으면 잘못된 태그로 레이아웃 파일을 덮어쓰게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-134">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="0826a-135">기존 *\_레이아웃* 을 선택한 경우에는이 파일을 덮어쓰지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="0826a-135">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="0826a-136">예: MVC 프로젝트에 대 한 Razor Pages `~/Views/Shared/_Layout.cshtml`에 대 한 `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="0826a-136">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="0826a-137">기존 데이터 컨텍스트를 사용하려면 재정의할 파일을 하나 이상 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-137">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="0826a-138">데이터 컨텍스트를 추가하려면 파일을 하나 이상 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-138">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="0826a-139">데이터 컨텍스트 클래스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-139">Select your data context class.</span></span>
  * <span data-ttu-id="0826a-140">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-140">Select **Add**.</span></span>
* <span data-ttu-id="0826a-141">새 사용자 컨텍스트를 만들고 Identity에 대한 사용자 지정 사용자 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-141">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="0826a-142">**+** 단추를 선택 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-142">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="0826a-143">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-143">Select **Add**.</span></span>

<span data-ttu-id="0826a-144">참고: 새 사용자 컨텍스트를 만드는 경우 재정의할 파일을 선택할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-144">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0826a-145">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0826a-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0826a-146">ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-146">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="0826a-147">[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 프로젝트 (\*.csproj) 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-147">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="0826a-148">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-148">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="0826a-149">Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-149">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="0826a-150">프로젝트 폴더에서 원하는 옵션을 사용하여 Identity 스캐폴더를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-150">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="0826a-151">예를 들어 기본 UI와 최소 파일 수를 사용하여 Identity를 설정하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-151">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="0826a-152">DB 컨텍스트에 대한 올바른 정규화된 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-152">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="0826a-153">PowerShell은 세미콜론을 명령 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-153">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="0826a-154">PowerShell을 사용하는 경우 파일 목록에서 세미콜론을 이스케이프하거나 파일 목록을 큰따옴표로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-154">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="0826a-155">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-155">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="0826a-156">`--files` 플래그 또는 `--useDefaultUI` 플래그를 지정 하지 않고 Id 스 캐 폴더를 실행 하면 사용 가능한 모든 Id UI 페이지가 프로젝트에 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0826a-156">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end