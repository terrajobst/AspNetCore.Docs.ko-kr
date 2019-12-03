---
title: ASP.NET Core 프로젝트에서 Id에 사용자 데이터 추가, 다운로드 및 삭제
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 추가 하는 방법을 알아봅니다. GDPR 당 데이터를 삭제 합니다.
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 6daca5776930f80eec8d81132b5a5c4d4d5c13ad
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681164"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="b6646-104">ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터 추가, 다운로드 및 삭제</span><span class="sxs-lookup"><span data-stu-id="b6646-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="b6646-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b6646-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b6646-106">이 문서에서는 다음을 수행 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-106">This article shows how to:</span></span>

* <span data-ttu-id="b6646-107">ASP.NET Core 웹 앱에 사용자 지정 사용자 데이터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="b6646-108">사용자 지정 사용자 데이터 모델을 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 특성으로 데코레이팅 하 여 자동으로 다운로드 및 삭제에 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-108">Decorate the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="b6646-109">데이터를 다운로드 하 고 삭제할 수 있도록 하는 것은 [Gdpr](xref:security/gdpr) 요구 사항을 충족 하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="b6646-110">프로젝트 샘플은 Razor Pages 웹 앱에서 만들지만 ASP.NET Core MVC 웹 앱에 대 한 지침과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="b6646-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b6646-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b6646-112">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="b6646-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="b6646-113">Razor 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="b6646-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6646-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6646-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b6646-115">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="b6646-116">[다운로드 샘플](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임 스페이스와 일치 시키려는 경우 프로젝트 이름을 **WebApp1** 로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="b6646-117">**ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="b6646-118">드롭다운에서 **ASP.NET Core 3.0** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="b6646-119">**웹 응용 프로그램** 선택 > **확인**</span><span class="sxs-lookup"><span data-stu-id="b6646-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="b6646-120">프로젝트를 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="b6646-121">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="b6646-122">[다운로드 샘플](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임 스페이스와 일치 시키려는 경우 프로젝트 이름을 **WebApp1** 로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="b6646-123">**ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="b6646-124">드롭다운에서 **ASP.NET Core 2.2** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="b6646-125">**웹 응용 프로그램** 선택 > **확인**</span><span class="sxs-lookup"><span data-stu-id="b6646-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="b6646-126">프로젝트를 빌드 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b6646-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b6646-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="b6646-128">Id 스 캐 폴더 실행</span><span class="sxs-lookup"><span data-stu-id="b6646-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6646-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6646-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b6646-130">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **새 스 캐 폴드 항목** > **추가** 를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b6646-131">**스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 **id** > **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="b6646-132">**Id 추가** 대화 상자에서 다음 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-132">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="b6646-133">기존 레이아웃 파일 *~/Pages/Shared/_Layout를 선택 합니다.*</span><span class="sxs-lookup"><span data-stu-id="b6646-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="b6646-134">재정의할 다음 파일 선택:</span><span class="sxs-lookup"><span data-stu-id="b6646-134">Select the following files to override:</span></span>
    * <span data-ttu-id="b6646-135">**계정/등록**</span><span class="sxs-lookup"><span data-stu-id="b6646-135">**Account/Register**</span></span>
    * <span data-ttu-id="b6646-136">**계정/관리/인덱스**</span><span class="sxs-lookup"><span data-stu-id="b6646-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="b6646-137">**+** 단추를 선택 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="b6646-138">프로젝트 이름이 **WebApp1**인 경우 형식 (**WebApp1. WebApp1Context** )을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="b6646-139">**+** 단추를 선택 하 여 새 **사용자 클래스**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="b6646-140">형식 (프로젝트 이름이 **WebApp1**인 경우**WebApp1User** )을 적용 > **추가**합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="b6646-141">**추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-141">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b6646-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b6646-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b6646-143">이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="b6646-144">프로젝트 (.csproj) 파일에 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="b6646-145">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="b6646-146">다음 명령을 실행 하 여 Identity 스 캐 폴더 옵션을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="b6646-147">프로젝트 폴더에서 Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="b6646-148">[마이그레이션, UseAuthentication 및 레이아웃](xref:security/authentication/scaffold-identity#efm) 의 지침에 따라 다음 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="b6646-149">마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="b6646-150">`UseAuthentication`를 `Startup.Configure`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="b6646-151">레이아웃 파일에 `<partial name="_LoginPartial" />`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="b6646-152">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-152">Test the app:</span></span>
  * <span data-ttu-id="b6646-153">사용자 등록</span><span class="sxs-lookup"><span data-stu-id="b6646-153">Register a user</span></span>
  * <span data-ttu-id="b6646-154">새 사용자 이름 ( **로그 아웃** 링크 옆에 있는)을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="b6646-155">창을 확장 하거나 탐색 모음 아이콘을 선택 하 여 사용자 이름 및 기타 링크를 표시 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="b6646-156">**개인 데이터** 탭을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="b6646-157">**다운로드** 단추를 선택 하 고 *PersonalData* 파일을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="b6646-158">로그온 한 사용자를 삭제 하는 **삭제** 단추를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="b6646-159">Id DB에 사용자 지정 사용자 데이터 추가</span><span class="sxs-lookup"><span data-stu-id="b6646-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="b6646-160">`IdentityUser` 파생 클래스를 사용자 지정 속성으로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="b6646-161">WebApp1 프로젝트의 이름을 지정 하는 경우 파일의 이름은 *Areas/Identity/Data/WebApp1User*입니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="b6646-162">다음 코드를 사용 하 여 파일을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="b6646-163">[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) 특성으로 데코레이팅된 속성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-163">Properties decorated with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="b6646-164">*영역/i d/페이지/계정/관리/DeletePersonalData* Razor 페이지에서 `UserManager.Delete`를 호출 하면 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="b6646-165">*영역/i d/페이지/계정/관리/DownloadPersonalData* Razor 페이지를 통해 다운로드 한 데이터에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="b6646-166">계정/관리/인덱스를 업데이트 합니다. cshtml 페이지</span><span class="sxs-lookup"><span data-stu-id="b6646-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="b6646-167">다음 강조 표시 된 코드를 사용 하 여 *영역/i d/페이지/계정/관리/* r e p. s s t e m. s s o l e s `InputModel`</span><span class="sxs-lookup"><span data-stu-id="b6646-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="b6646-168">다음 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/관리/인덱스를 업데이트 합니다.*</span><span class="sxs-lookup"><span data-stu-id="b6646-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="b6646-169">다음 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/관리/인덱스를 업데이트 합니다.*</span><span class="sxs-lookup"><span data-stu-id="b6646-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="b6646-170">계정/레지스터를 업데이트 합니다. cshtml 페이지</span><span class="sxs-lookup"><span data-stu-id="b6646-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="b6646-171">다음 강조 표시 된 코드를 사용 하 여 *영역/i d/페이지/계정/* s a s e. s s o. s s o. s s o `InputModel`</span><span class="sxs-lookup"><span data-stu-id="b6646-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="b6646-172">다음과 같이 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/레지스터* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="b6646-173">다음과 같이 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/레지스터* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="b6646-174">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="b6646-175">사용자 지정 사용자 데이터에 대 한 마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="b6646-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b6646-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6646-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b6646-177">Visual Studio **패키지 관리자 콘솔**에서 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b6646-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b6646-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="b6646-179">사용자 지정 사용자 데이터 만들기, 보기, 다운로드, 삭제 테스트</span><span class="sxs-lookup"><span data-stu-id="b6646-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="b6646-180">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-180">Test the app:</span></span>

* <span data-ttu-id="b6646-181">새 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-181">Register a new user.</span></span>
* <span data-ttu-id="b6646-182">`/Identity/Account/Manage` 페이지에서 사용자 지정 사용자 데이터를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="b6646-183">`/Identity/Account/Manage/PersonalData` 페이지에서 사용자 개인 데이터를 다운로드 하 여 봅니다.</span><span class="sxs-lookup"><span data-stu-id="b6646-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
