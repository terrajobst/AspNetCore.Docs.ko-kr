---
title: 추가, 다운로드 및 ASP.NET Core 프로젝트의 Id로 사용자 데이터를 삭제 합니다.
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 추가 하는 방법에 알아봅니다. GDPR에 따라 데이터를 삭제 합니다.
ms.author: riande
ms.date: 12/05/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: f54df68834cd3e2493e558aaab9851f036f3f01b
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880749"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="3f49c-104">추가, 다운로드 및 ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="3f49c-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3f49c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3f49c-106">이 아티클에서 방법.</span><span class="sxs-lookup"><span data-stu-id="3f49c-106">This article shows how to:</span></span>

* <span data-ttu-id="3f49c-107">ASP.NET Core 웹 앱에 사용자 지정 사용자 데이터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="3f49c-108">사용자 지정 사용자 데이터 모델을 자동으로 다운로드 및 삭제에 사용할 수 있도록 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 특성으로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="3f49c-109">충족 하는 사용 하면 데이터를 다운로드 하 고 삭제 하는 일을 할 [GDPR](xref:security/gdpr) 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="3f49c-110">프로젝트 샘플에는 Razor 페이지 웹 앱에서 만들어지지만 지침은 ASP.NET Core MVC 웹 앱에 대해 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="3f49c-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f49c-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f49c-112">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="3f49c-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="3f49c-113">Razor 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="3f49c-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3f49c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f49c-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="3f49c-115">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="3f49c-116">프로젝트 이름을 **WebApp1** 되도록 하려는 경우의 네임 스페이스와 일치 합니다 [샘플을 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="3f49c-117">**ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="3f49c-118">드롭다운에서 **ASP.NET Core 3.0** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="3f49c-119">**웹 응용 프로그램** 선택 > **확인**</span><span class="sxs-lookup"><span data-stu-id="3f49c-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="3f49c-120">프로젝트를 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="3f49c-121">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="3f49c-122">프로젝트 이름을 **WebApp1** 되도록 하려는 경우의 네임 스페이스와 일치 합니다 [샘플을 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="3f49c-123">**ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="3f49c-124">드롭다운에서 **ASP.NET Core 2.2** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="3f49c-125">**웹 응용 프로그램** 선택 > **확인**</span><span class="sxs-lookup"><span data-stu-id="3f49c-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="3f49c-126">프로젝트를 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3f49c-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3f49c-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="3f49c-128">Identity 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3f49c-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f49c-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3f49c-130">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **추가** > **스 캐 폴드 된 새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3f49c-131">왼쪽된 창에서 합니다 **스 캐 폴드 추가** 대화 상자에서 **Identity** > **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="3f49c-132">에 **ADD Id** 대화 상자에서 다음 옵션:</span><span class="sxs-lookup"><span data-stu-id="3f49c-132">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="3f49c-133">기존 레이아웃 파일 선택 *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3f49c-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="3f49c-134">재정의 하려면 다음 파일을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-134">Select the following files to override:</span></span>
    * <span data-ttu-id="3f49c-135">**계정/등록**</span><span class="sxs-lookup"><span data-stu-id="3f49c-135">**Account/Register**</span></span>
    * <span data-ttu-id="3f49c-136">**계정 / / 인덱스 관리**</span><span class="sxs-lookup"><span data-stu-id="3f49c-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="3f49c-137">선택 된 **+** 새 단추 **데이터 컨텍스트 클래스**합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="3f49c-138">형식을 수락 (**WebApp1.Models.WebApp1Context** 프로젝트의 이름이 **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="3f49c-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="3f49c-139">선택 된 **+** 새 단추 **사용자 클래스**합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="3f49c-140">형식을 수락 (**WebApp1User** 프로젝트의 이름이 **WebApp1**) > **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="3f49c-141">선택 **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-141">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3f49c-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3f49c-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3f49c-143">ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="3f49c-144">에 대 한 패키지 참조 추가 [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 프로젝트 (.csproj) 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="3f49c-145">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="3f49c-146">Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="3f49c-147">프로젝트 폴더에서 Identity 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="3f49c-148">지침에 따라 [마이그레이션과 UseAuthentication, 레이아웃](xref:security/authentication/scaffold-identity#efm) 다음 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="3f49c-149">마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="3f49c-150">`UseAuthentication`를 `Startup.Configure`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="3f49c-151">추가 `<partial name="_LoginPartial" />` 레이아웃 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="3f49c-152">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-152">Test the app:</span></span>
  * <span data-ttu-id="3f49c-153">사용자 등록</span><span class="sxs-lookup"><span data-stu-id="3f49c-153">Register a user</span></span>
  * <span data-ttu-id="3f49c-154">새 사용자 이름 선택 (옆에 **로그 아웃** 링크).</span><span class="sxs-lookup"><span data-stu-id="3f49c-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="3f49c-155">창이 확장 또는 사용자 이름 및 다른 링크를 표시 하려면 탐색 모음 아이콘을 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="3f49c-156">선택 된 **개인 데이터** 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="3f49c-157">선택 합니다 **다운로드** 단추를 검사 합니다 *PersonalData.json* 파일.</span><span class="sxs-lookup"><span data-stu-id="3f49c-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="3f49c-158">테스트를 **삭제** 단추를 사용자에서 로그인을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="3f49c-159">Identity DB 사용자 지정 데이터 추가</span><span class="sxs-lookup"><span data-stu-id="3f49c-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="3f49c-160">업데이트 된 `IdentityUser` 사용자 지정 속성을 사용 하 여 클래스를 파생 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="3f49c-161">파일 이름은 프로젝트 구성이 WebApp1 라는 하는 경우 *Areas/Identity/Data/WebApp1User.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="3f49c-162">다음 코드를 사용 하 여 파일을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="3f49c-163">[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) 특성이 있는 속성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="3f49c-164">삭제 된 *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor 페이지 호출 `UserManager.Delete`합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="3f49c-165">다운로드 한 데이터에 포함 된 *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor 페이지.</span><span class="sxs-lookup"><span data-stu-id="3f49c-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="3f49c-166">Account/Manage/Index.cshtml 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="3f49c-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="3f49c-167">업데이트를 `InputModel` 에 *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* 강조 표시 된 코드를 다음으로:</span><span class="sxs-lookup"><span data-stu-id="3f49c-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="3f49c-168">업데이트를 *Areas/Identity/Pages/Account/Manage/Index.cshtml* 다음 강조 표시 된 태그를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="3f49c-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="3f49c-169">업데이트를 *Areas/Identity/Pages/Account/Manage/Index.cshtml* 다음 강조 표시 된 태그를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="3f49c-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="3f49c-170">Account/Register.cshtml 페이지가 업데이트</span><span class="sxs-lookup"><span data-stu-id="3f49c-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="3f49c-171">업데이트를 `InputModel` 에 *Areas/Identity/Pages/Account/Register.cshtml.cs* 강조 표시 된 코드를 다음으로:</span><span class="sxs-lookup"><span data-stu-id="3f49c-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="3f49c-172">업데이트를 *Areas/Identity/Pages/Account/Register.cshtml* 다음 강조 표시 된 태그를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="3f49c-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="3f49c-173">업데이트를 *Areas/Identity/Pages/Account/Register.cshtml* 다음 강조 표시 된 태그를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="3f49c-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="3f49c-174">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="3f49c-175">사용자 지정 사용자 데이터에 대 한 마이그레이션을 추가합니다</span><span class="sxs-lookup"><span data-stu-id="3f49c-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3f49c-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f49c-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f49c-177">Visual studio에서 **패키지 관리자 콘솔**:</span><span class="sxs-lookup"><span data-stu-id="3f49c-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3f49c-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3f49c-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="3f49c-179">테스트 만들기, 보기, 다운로드, 사용자 지정 사용자 데이터를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="3f49c-180">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-180">Test the app:</span></span>

* <span data-ttu-id="3f49c-181">새 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-181">Register a new user.</span></span>
* <span data-ttu-id="3f49c-182">사용자 지정 사용자 데이터를 보려면를 `/Identity/Account/Manage` 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="3f49c-183">다운로드 하 고 사용자 개인 데이터를 볼는 `/Identity/Account/Manage/PersonalData` 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="3f49c-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
