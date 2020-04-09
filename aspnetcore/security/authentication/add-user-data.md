---
title: ASP.NET 핵심 프로젝트에서 ID에 사용자 데이터 추가, 다운로드 및 삭제
author: rick-anderson
description: ASP.NET Core 프로젝트에서 ID에 사용자 지정 사용자 데이터를 추가하는 방법을 알아봅니다. GDPR당 데이터를 삭제합니다.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501224"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="5cbde-104">ASP.NET 핵심 프로젝트에서 ID에 사용자 지정 사용자 데이터를 추가, 다운로드 및 삭제</span><span class="sxs-lookup"><span data-stu-id="5cbde-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="5cbde-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5cbde-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5cbde-106">이 문서는 다음 방법을 안내합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-106">This article shows how to:</span></span>

* <span data-ttu-id="5cbde-107">사용자 지정 사용자 데이터를 ASP.NET Core 웹 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="5cbde-108">사용자 지정 사용자 데이터 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 모델을 특성에 표시하여 다운로드 및 삭제를 자동으로 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="5cbde-109">데이터를 다운로드하고 삭제할 수 있도록 하면 [GDPR](xref:security/gdpr) 요구 사항을 충족하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="5cbde-110">프로젝트 샘플은 Razor Pages 웹 앱에서 만들어지지만 지침은 ASP.NET 코어 MVC 웹 앱과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="5cbde-111">[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) [방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5cbde-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5cbde-112">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5cbde-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="5cbde-113">Razor 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="5cbde-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5cbde-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cbde-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="5cbde-115">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="5cbde-116">[다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임스페이스와 일치하려면 프로젝트 **WebApp1의** 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="5cbde-117">**ASP.NET 핵심 웹 응용 프로그램** > **확인** 선택</span><span class="sxs-lookup"><span data-stu-id="5cbde-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="5cbde-118">드롭다운에서 **ASP.NET 코어 3.0** 선택</span><span class="sxs-lookup"><span data-stu-id="5cbde-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="5cbde-119">**웹 응용 프로그램** > **확인** 선택</span><span class="sxs-lookup"><span data-stu-id="5cbde-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="5cbde-120">프로젝트를 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="5cbde-121">Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="5cbde-122">[다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임스페이스와 일치하려면 프로젝트 **WebApp1의** 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="5cbde-123">**ASP.NET 핵심 웹 응용 프로그램** > **확인** 선택</span><span class="sxs-lookup"><span data-stu-id="5cbde-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="5cbde-124">드롭다운에서 **코어 ASP.NET 2.2** 선택</span><span class="sxs-lookup"><span data-stu-id="5cbde-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="5cbde-125">**웹 응용 프로그램** > **확인** 선택</span><span class="sxs-lookup"><span data-stu-id="5cbde-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="5cbde-126">프로젝트를 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="5cbde-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5cbde-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="5cbde-128">ID 폴더 실행</span><span class="sxs-lookup"><span data-stu-id="5cbde-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5cbde-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cbde-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5cbde-130">**솔루션 탐색기에서**프로젝트를 마우스 오른쪽 단추로 클릭하여**새 스캐폴드 항목** **추가** > 를 >.</span><span class="sxs-lookup"><span data-stu-id="5cbde-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5cbde-131">**스캐폴드 추가** 대화 상자의 왼쪽 창에서 **ID** > **추가를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="5cbde-132">ID **추가** 대화 상자에서 다음 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="5cbde-133">기존 레이아웃 파일 *~/페이지/공유/_Layout.cshtml을* 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="5cbde-134">재정의할 다음 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-134">Select the following files to override:</span></span>
    * <span data-ttu-id="5cbde-135">**계정/등록**</span><span class="sxs-lookup"><span data-stu-id="5cbde-135">**Account/Register**</span></span>
    * <span data-ttu-id="5cbde-136">**계정/관리/색인**</span><span class="sxs-lookup"><span data-stu-id="5cbde-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="5cbde-137">단추를 **+** 선택하여 새 **데이터 컨텍스트 클래스를**만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="5cbde-138">형식**수락(WebApp1.Models.WebApp1Context** 프로젝트의 이름이 **WebApp1인**경우 컨텍스트).</span><span class="sxs-lookup"><span data-stu-id="5cbde-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="5cbde-139">단추를 **+** 선택하여 새 **사용자 클래스를**만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="5cbde-140">형식 수락 **(WebApp1User** 프로젝트의 이름이 **WebApp1)**> **추가**.</span><span class="sxs-lookup"><span data-stu-id="5cbde-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="5cbde-141">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5cbde-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5cbde-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5cbde-143">이전에 ASP.NET 코어 scaffolder를 설치하지 않은 경우 지금 설치하십시오.</span><span class="sxs-lookup"><span data-stu-id="5cbde-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="5cbde-144">프로젝트(.csproj) 파일에 [Microsoft.VisualStudio.Web.CodeGeneration.Design에](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="5cbde-145">프로젝트 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="5cbde-146">다음 명령을 실행하여 Id scaffolder 옵션을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="5cbde-147">프로젝트 폴더에서 ID scaffolder를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="5cbde-148">[마이그레이션, 사용 인증 및 레이아웃의](xref:security/authentication/scaffold-identity#efm) 지침에 따라 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="5cbde-149">마이그레이션을 만들고 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="5cbde-150">`UseAuthentication`을 `Startup.Configure`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="5cbde-151">레이아웃 `<partial name="_LoginPartial" />` 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="5cbde-152">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-152">Test the app:</span></span>
  * <span data-ttu-id="5cbde-153">사용자 등록</span><span class="sxs-lookup"><span data-stu-id="5cbde-153">Register a user</span></span>
  * <span data-ttu-id="5cbde-154">**로그아웃** 링크 옆의 새 사용자 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="5cbde-155">사용자 이름 및 기타 링크를 표시하려면 창을 확장하거나 탐색 모음 아이콘을 선택해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="5cbde-156">개인 **데이터** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="5cbde-157">**다운로드** 단추를 선택하고 *PersonalData.json* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="5cbde-158">로그온한 사용자를 삭제하는 **삭제** 단추를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="5cbde-159">ID DB에 사용자 지정 사용자 데이터 추가</span><span class="sxs-lookup"><span data-stu-id="5cbde-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="5cbde-160">사용자 `IdentityUser` 지정 속성으로 파생 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="5cbde-161">프로젝트 WebApp1의 이름을 지정한 경우 파일의 이름이 *영역/ID/데이터/WebApp1User.cs입니다.*</span><span class="sxs-lookup"><span data-stu-id="5cbde-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="5cbde-162">다음 코드로 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="5cbde-163">[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) 특성이 있는 속성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="5cbde-164">*영역/ID/페이지/계정/관리/삭제개인데이터.cshtml* 면도기 페이지가 `UserManager.Delete`호출될 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="5cbde-165">*영역 / ID / 페이지 / 계정 / 관리 / 다운로드개인 데이터.cshtml* 면도기 페이지에 의해 다운로드 된 데이터에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="5cbde-166">계정/관리/Index.cshtml 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="5cbde-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="5cbde-167">다음 `InputModel` 강조 표시된 코드로 *영역/ID/페이지/계정/관리/Index.cshtml.cs.cs.cs에서* 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="5cbde-168">다음 강조 표시된 태그로 *영역/ID/페이지/계정/관리/Index.cshtml을* 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="5cbde-169">다음 강조 표시된 태그로 *영역/ID/페이지/계정/관리/Index.cshtml을* 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="5cbde-170">계정/Register.cshtml 페이지 업데이트</span><span class="sxs-lookup"><span data-stu-id="5cbde-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="5cbde-171">`InputModel` 다음 강조 표시된 코드로 *영역/ID/페이지/계정/Register.cshtml.cs에서* 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="5cbde-172">다음 강조 표시된 태그로 *영역/ID/페이지/계정/Register.cshtml을* 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="5cbde-173">다음 강조 표시된 태그로 *영역/ID/페이지/계정/Register.cshtml을* 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="5cbde-174">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="5cbde-175">사용자 지정 사용자 데이터에 대한 마이그레이션 추가</span><span class="sxs-lookup"><span data-stu-id="5cbde-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5cbde-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cbde-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5cbde-177">비주얼 스튜디오 **패키지 관리자 콘솔에서**:</span><span class="sxs-lookup"><span data-stu-id="5cbde-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5cbde-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5cbde-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="5cbde-179">사용자 지정 사용자 데이터 만들기, 보기, 다운로드, 삭제</span><span class="sxs-lookup"><span data-stu-id="5cbde-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="5cbde-180">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-180">Test the app:</span></span>

* <span data-ttu-id="5cbde-181">새 사용자를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-181">Register a new user.</span></span>
* <span data-ttu-id="5cbde-182">페이지에서 사용자 지정 사용자 `/Identity/Account/Manage` 데이터를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="5cbde-183">페이지에서 사용자 개인 데이터를 다운로드하고 봅니다. `/Identity/Account/Manage/PersonalData`</span><span class="sxs-lookup"><span data-stu-id="5cbde-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="5cbde-184">IUser클레임스프렉토리를 사용하여 ID에 클레임 추가<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="5cbde-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="5cbde-185">인터페이스를 사용하여 ASP.NET 코어 ID에 `IUserClaimsPrincipalFactory<T>` 추가 클레임을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="5cbde-186">이 클래스는 `Startup.ConfigureServices` 메서드의 앱에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5cbde-187">다음과 같이 클래스의 사용자 지정 구현을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="5cbde-188">데모 코드는 `ApplicationUser` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="5cbde-189">이 클래스는 `IsAdmin` 추가 클레임을 추가하는 데 사용되는 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="5cbde-190">`AdditionalUserClaimsPrincipalFactory`는 `UserClaimsPrincipalFactory` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="5cbde-191">에 새 역할 클레임이 `ClaimsPrincipal`추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="5cbde-192">그런 다음 추가 클레임을 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="5cbde-193">Razor 페이지에서 인스턴스를 `IAuthorizationService` 사용하여 클레임 값에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5cbde-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
