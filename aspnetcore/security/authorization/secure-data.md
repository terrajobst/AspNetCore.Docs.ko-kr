---
title: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기
author: rick-anderson
description: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 Razor 페이지 앱을 만드는 방법에 알아봅니다. HTTPS, 인증, 보안, ASP.NET Core Id를 포함합니다.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 7710a8965771db02e601dafb7da752906bcd43e5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651921"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="e7ef4-104">권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="e7ef4-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="e7ef4-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="e7ef4-106">ASP.NET Core MVC 버전은 [이 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="e7ef4-107">이 자습서의 ASP.NET Core 1.1 버전은 [이](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="e7ef4-108">1\.1 ASP.NET Core 샘플은 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e7ef4-109">[이 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) 보기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7ef4-110">이 자습서에는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="e7ef4-111">인증 된 (등록 된) 사용자는 연락처 목록에 표시를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="e7ef4-112">세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-112">There are three security groups:</span></span>

* <span data-ttu-id="e7ef4-113">**등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="e7ef4-114">**관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="e7ef4-115">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="e7ef4-116">**관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="e7ef4-117">이 문서의 이미지는 최신 템플릿과 정확히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="e7ef4-118">다음 이미지에서는 사용자 Rick (`rick@example.com`)가 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="e7ef4-119">Rick는 승인 된 연락처만 볼 수 있고 **편집**/**삭제**/연락처에 대 한 새 링크를 **만들** 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="e7ef4-120">Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="e7ef4-121">다른 사용자에 게는 관리자가 "승인 됨" 상태 변경 될 때까지 마지막 레코드를 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![로그인 한 Rick을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="e7ef4-123">다음 그림에서 `manager@contoso.com`는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![로그인 manager@contoso.com를 보여 주는 스크린샷](secure-data/_static/manager1.png)

<span data-ttu-id="e7ef4-125">다음 이미지는 관리자의 연락처 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-125">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자의 보기](secure-data/_static/manager.png)

<span data-ttu-id="e7ef4-127">**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="e7ef4-128">다음 그림에서 `admin@contoso.com`는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![로그인 admin@contoso.com를 보여 주는 스크린샷](secure-data/_static/admin.png)

<span data-ttu-id="e7ef4-130">관리자는 모든 권한을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-130">The administrator has all privileges.</span></span> <span data-ttu-id="e7ef4-131">그녀는 연락처 읽기/편집/삭제할 수 및 연락처의 상태를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="e7ef4-132">앱은 다음 `Contact` 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="e7ef4-133">다음 인증 처리기를 포함 하는 샘플:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="e7ef4-134">`ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="e7ef4-135">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="e7ef4-136">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e7ef4-137">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="e7ef4-137">Prerequisites</span></span>

<span data-ttu-id="e7ef4-138">이 자습서 고급 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-138">This tutorial is advanced.</span></span> <span data-ttu-id="e7ef4-139">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-139">You should be familiar with:</span></span>

* [<span data-ttu-id="e7ef4-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ef4-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="e7ef4-141">인증</span><span class="sxs-lookup"><span data-stu-id="e7ef4-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="e7ef4-142">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="e7ef4-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="e7ef4-143">권한 부여</span><span class="sxs-lookup"><span data-stu-id="e7ef4-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="e7ef4-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e7ef4-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="e7ef4-145">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="e7ef4-145">The starter and completed app</span></span>

<span data-ttu-id="e7ef4-146">[완성](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="e7ef4-147">보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="e7ef4-148">시작 앱</span><span class="sxs-lookup"><span data-stu-id="e7ef4-148">The starter app</span></span>

<span data-ttu-id="e7ef4-149">[시작](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="e7ef4-150">앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="e7ef4-151">사용자 데이터 보호</span><span class="sxs-lookup"><span data-stu-id="e7ef4-151">Secure user data</span></span>

<span data-ttu-id="e7ef4-152">다음 섹션에서는 모든 주요 단계가 보안 사용자 데이터 앱 만들기 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="e7ef4-153">완료 된 프로젝트 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="e7ef4-154">사용자에 게 연락 데이터를 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-154">Tie the contact data to the user</span></span>

<span data-ttu-id="e7ef4-155">ASP.NET [id](xref:security/authentication/identity) 사용자 id를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="e7ef4-156">`Contact` 모델에 `OwnerID` 및 `ContactStatus`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="e7ef4-157">`OwnerID`는 [id](xref:security/authentication/identity) 데이터베이스의 `AspNetUser` 테이블에서 가져온 사용자의 id입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="e7ef4-158">`Status` 필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="e7ef4-159">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="e7ef4-160">Id에 역할 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="e7ef4-160">Add Role services to Identity</span></span>

<span data-ttu-id="e7ef4-161">역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="e7ef4-162">인증 된 사용자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-162">Require authenticated users</span></span>

<span data-ttu-id="e7ef4-163">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="e7ef4-164">`[AllowAnonymous]` 특성을 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃 (opt out) 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="e7ef4-165">새로 추가 된 Razor 페이지 및 컨트롤러를 보호 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="e7ef4-166">기본적으로 인증을 요구 하는 것은 새 컨트롤러에 의존 하는 것 보다 더 안전 하며, Razor Pages `[Authorize]` 특성을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="e7ef4-167">익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스 및 개인 정보 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="e7ef4-168">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="e7ef4-168">Configure the test account</span></span>

<span data-ttu-id="e7ef4-169">`SeedData` 클래스는 관리자와 관리자 라는 두 개의 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="e7ef4-170">암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="e7ef4-171">프로젝트 디렉터리 ( *Program.cs*가 포함 된 디렉터리)에서 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="e7ef4-172">강력한 암호를 지정 하지 않으면 `SeedData.Initialize`를 호출할 때 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="e7ef4-173">테스트 암호를 사용 하도록 `Main`를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="e7ef4-174">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="e7ef4-175">`SeedData` 클래스에서 `Initialize` 메서드를 업데이트 하 여 테스트 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="e7ef4-176">관리자 사용자 ID를 추가 하 고 연락처에 `ContactStatus` 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="e7ef4-177">"제출" 및 "Rejected" 하나의 연락처 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="e7ef4-178">사용자 ID 및 상태를 모든 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="e7ef4-179">하나만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="e7ef4-180">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="e7ef4-181">*권한 부여* 폴더에 `ContactIsOwnerAuthorizationHandler` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7ef4-182">`ContactIsOwnerAuthorizationHandler`는 리소스에서 사용 하는 사용자가 리소스를 소유 하 고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="e7ef4-183">`ContactIsOwnerAuthorizationHandler` 컨텍스트를 호출 합니다 [. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="e7ef4-184">권한 부여 처리기 일반적으로:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="e7ef4-185">요구 사항이 충족 되 면 `context.Succeed`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="e7ef4-186">요구 사항이 충족 되지 않은 경우 `Task.CompletedTask`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="e7ef4-187">`Task.CompletedTask`는 성공 또는 실패&mdash;다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="e7ef4-188">명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="e7ef4-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="e7ef4-189">앱 편집/삭제/만들려면 연락처 소유자가 자신의 데이터를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="e7ef4-190">`ContactIsOwnerAuthorizationHandler`는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="e7ef4-191">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-191">Create a manager authorization handler</span></span>

<span data-ttu-id="e7ef4-192">*권한 부여* 폴더에 `ContactManagerAuthorizationHandler` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7ef4-193">`ContactManagerAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="e7ef4-194">관리자만 승인 하거나 (새롭거나 변경 된) 콘텐츠 변경 내용을 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="e7ef4-195">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="e7ef4-196">*권한 부여* 폴더에 `ContactAdministratorsAuthorizationHandler` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7ef4-197">`ContactAdministratorsAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="e7ef4-198">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="e7ef4-199">인증 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-199">Register the authorization handlers</span></span>

<span data-ttu-id="e7ef4-200">[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="e7ef4-201">`ContactIsOwnerAuthorizationHandler`은 Entity Framework Core을 기반으로 하는 ASP.NET Core [id](xref:security/authentication/identity)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="e7ef4-202">[종속성 주입](xref:fundamentals/dependency-injection)을 통해 `ContactsController`에서 사용할 수 있도록 서비스 컬렉션에 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e7ef4-203">`ConfigureServices`의 끝에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="e7ef4-204">`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="e7ef4-205">단일 항목는 EF를 사용 하지 않고 필요한 모든 정보는 `HandleRequirementAsync` 메서드의 `Context` 매개 변수에 있기 때문에 더 이상 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="e7ef4-206">권한 부여를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-206">Support authorization</span></span>

<span data-ttu-id="e7ef4-207">이 섹션에서는 Razor 페이지를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="e7ef4-208">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="e7ef4-209">`ContactOperations` 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="e7ef4-210">이 클래스는 포함 요구 사항을 지 원하는:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="e7ef4-211">연락처 Razor 페이지에 대 한 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="e7ef4-212">연락처 Razor 페이지에에서 사용 된 서비스를 포함 하는 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="e7ef4-213">초기화 코드를 한 위치에 배치 하는 기본 클래스:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="e7ef4-214">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-214">The preceding code:</span></span>

* <span data-ttu-id="e7ef4-215">권한 부여 처리기에 액세스 하는 `IAuthorizationService` 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="e7ef4-216">Id `UserManager` 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="e7ef4-217">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="e7ef4-218">업데이트 된 CreateModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-218">Update the CreateModel</span></span>

<span data-ttu-id="e7ef4-219">`DI_BasePageModel` 기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="e7ef4-220">`CreateModel.OnPostAsync` 메서드를 다음으로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="e7ef4-221">사용자 ID를 `Contact` 모델에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="e7ef4-222">사용자에 게 연락처를 만들 수 있는 권한을 확인 하는 권한 부여 처리기를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="e7ef4-223">업데이트 된 IndexModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-223">Update the IndexModel</span></span>

<span data-ttu-id="e7ef4-224">승인 된 연락처만 일반 사용자에 게 표시 되도록 `OnGetAsync` 메서드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="e7ef4-225">업데이트 된 EditModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-225">Update the EditModel</span></span>

<span data-ttu-id="e7ef4-226">사용자가 연락처를 소유를 확인 하는 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="e7ef4-227">리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="e7ef4-228">특성을 평가 하는 경우 앱 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="e7ef4-229">명령적 리소스 기반 권한 부여를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="e7ef4-230">페이지 모델에 로드 하 여 또는 자체 처리기 내에서 로드 하 여 앱에 리소스에 대 한 액세스 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="e7ef4-231">자주 리소스 키를 전달 하 여 리소스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="e7ef4-232">업데이트 된 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-232">Update the DeleteModel</span></span>

<span data-ttu-id="e7ef4-233">인증 처리기를 사용 하 여 사용자 연락처에 대 한 삭제 권한을 확인 하려면 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="e7ef4-234">권한 부여 서비스 보기 삽입</span><span class="sxs-lookup"><span data-stu-id="e7ef4-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="e7ef4-235">현재 UI은 편집 및 사용자가 수정할 수 없습니다. 연락처에 대 한 링크를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="e7ef4-236">모든 보기에서 사용할 수 있도록 *Pages/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="e7ef4-237">위의 태그는 여러 개의 `using` 문을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="e7ef4-238">적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e7ef4-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="e7ef4-239">데이터를 변경할 수 있는 권한이 없는 사용자에 게 링크를 숨기기는 앱을 보호 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="e7ef4-240">링크 숨기기는 올바른 링크를 표시 하 여 앱 보다 친숙 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="e7ef4-241">사용자가 편집을 호출 하 고 자신이 소유 하지 않는 데이터에 대 한 작업을 삭제 하도록 생성 된 Url hack 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="e7ef4-242">Razor 페이지 또는 컨트롤러에 데이터를 보호 하는 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="e7ef4-243">세부 정보 업데이트</span><span class="sxs-lookup"><span data-stu-id="e7ef4-243">Update Details</span></span>

<span data-ttu-id="e7ef4-244">관리자 승인 또는 연락처를 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="e7ef4-245">업데이트 세부 정보 페이지 모델:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="e7ef4-246">추가 하거나 역할에 사용자를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-246">Add or remove a user to a role</span></span>

<span data-ttu-id="e7ef4-247">에 대 한 자세한 내용은 다음 [문제](https://github.com/dotnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="e7ef4-248">사용자의 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-248">Removing privileges from a user.</span></span> <span data-ttu-id="e7ef4-249">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="e7ef4-250">사용자에 권한을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="e7ef4-251">챌린지와 금지 간의 차이점</span><span class="sxs-lookup"><span data-stu-id="e7ef4-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="e7ef4-252">이 앱은 인증 된 사용자를 [요구](#require-authenticated-users)하도록 기본 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="e7ef4-253">다음 코드는 익명 사용자를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-253">The following code allows anonymous users.</span></span> <span data-ttu-id="e7ef4-254">익명 사용자는 챌린지 vs 금지 간의 차이점을 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="e7ef4-255">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="e7ef4-255">In the preceding code:</span></span>

* <span data-ttu-id="e7ef4-256">사용자가 인증 **되지 않은** 경우 `ChallengeResult` 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="e7ef4-257">`ChallengeResult` 반환 되 면 사용자가 로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="e7ef4-258">사용자가 인증 되었지만 권한이 부여 되지 않은 경우 `ForbidResult` 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="e7ef4-259">`ForbidResult` 반환 되 면 사용자는 액세스 거부 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="e7ef4-260">완성된 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="e7ef4-260">Test the completed app</span></span>

<span data-ttu-id="e7ef4-261">시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="e7ef4-262">강력한 암호를 선택 합니다: 사용 하 여 8 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="e7ef4-263">예를 들어 `Passw0rd!`은 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="e7ef4-264">프로젝트의 폴더에서 다음 명령을 실행 합니다. 여기서 `<PW>`는 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="e7ef4-265">앱에 있는 경우 연락처:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-265">If the app has contacts:</span></span>

* <span data-ttu-id="e7ef4-266">`Contact` 테이블의 모든 레코드를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="e7ef4-267">데이터베이스를 시드하려면 앱을 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="e7ef4-268">완성된 된 앱을 테스트 하는 간편한 방법은 3 개의 서로 다른 브라우저 (또는 incognito/InPrivate 세션)을 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="e7ef4-269">한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="e7ef4-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="e7ef4-270">다른 사용자와 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="e7ef4-271">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-271">Verify the following operations:</span></span>

* <span data-ttu-id="e7ef4-272">등록 된 사용자의 모든 승인 된 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="e7ef4-273">등록 된 사용자 편집/삭제할 수 있습니다 자신의 고유 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="e7ef4-274">관리자 수 승인/거부할 연락처 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="e7ef4-275">`Details` 보기에는 **승인** 및 **거부** 단추가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="e7ef4-276">관리자 승인/거부를 편집/삭제할 모든 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="e7ef4-277">사용자</span><span class="sxs-lookup"><span data-stu-id="e7ef4-277">User</span></span>                | <span data-ttu-id="e7ef4-278">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="e7ef4-278">Seeded by the app</span></span> | <span data-ttu-id="e7ef4-279">옵션</span><span class="sxs-lookup"><span data-stu-id="e7ef4-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="e7ef4-280">예</span><span class="sxs-lookup"><span data-stu-id="e7ef4-280">No</span></span>                | <span data-ttu-id="e7ef4-281">자체 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="e7ef4-282">yes</span><span class="sxs-lookup"><span data-stu-id="e7ef4-282">Yes</span></span>               | <span data-ttu-id="e7ef4-283">승인/거부 하 고 편집/삭제할 데이터를 소유 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="e7ef4-284">yes</span><span class="sxs-lookup"><span data-stu-id="e7ef4-284">Yes</span></span>               | <span data-ttu-id="e7ef4-285">승인/거부 하 고 모든 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="e7ef4-286">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="e7ef4-287">삭제에 대 한 URL을 복사 하 고 관리자 연락처에서 편집 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="e7ef4-288">테스트 사용자를 이러한 작업을 수행할 수 없습니다 확인 하려면 테스트 사용자의 브라우저에 이러한 링크를 붙여 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="e7ef4-289">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-289">Create the starter app</span></span>

* <span data-ttu-id="e7ef4-290">"ContactManager" 라는 Razor 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="e7ef4-291">**개별 사용자 계정을**사용 하 여 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="e7ef4-292">네임 스페이스에는 샘플에 사용 된 네임 스페이스와 일치 하므로 "ContactManager" 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="e7ef4-293">`-uld`는 SQLite 대신 LocalDB를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="e7ef4-294">*모델/연락처를 추가 합니다. cs*:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="e7ef4-295">`Contact` 모델을 스 캐 폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="e7ef4-296">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="e7ef4-297">`dotnet aspnet-codegenerator razorpage` 명령을 사용 하 여 버그를 발생 하는 경우 [이 GitHub 문제](https://github.com/aspnet/Scaffolding/issues/984)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="e7ef4-298">*Pages/Shared/_Layout cshtml* 파일에서 지 각를 **업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e7ef4-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="e7ef4-299">만들기, 편집 및 연락처를 삭제 하 여 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="e7ef4-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="e7ef4-300">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="e7ef4-300">Seed the database</span></span>

<span data-ttu-id="e7ef4-301">*데이터* 폴더에 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="e7ef4-302">`Main`에서 `SeedData.Initialize`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="e7ef4-303">응용 프로그램 데이터베이스를 시드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-303">Test that the app seeded the database.</span></span> <span data-ttu-id="e7ef4-304">DB 연락처의 모든 행이 없으면 시드 메서드가 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="e7ef4-305">이 자습서에는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="e7ef4-306">인증 된 (등록 된) 사용자는 연락처 목록에 표시를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="e7ef4-307">세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-307">There are three security groups:</span></span>

* <span data-ttu-id="e7ef4-308">**등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="e7ef4-309">**관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="e7ef4-310">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="e7ef4-311">**관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="e7ef4-312">다음 이미지에서는 사용자 Rick (`rick@example.com`)가 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="e7ef4-313">Rick는 승인 된 연락처만 볼 수 있고 **편집**/**삭제**/연락처에 대 한 새 링크를 **만들** 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="e7ef4-314">Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="e7ef4-315">다른 사용자에 게는 관리자가 "승인 됨" 상태 변경 될 때까지 마지막 레코드를 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![로그인 한 Rick을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="e7ef4-317">다음 그림에서 `manager@contoso.com`는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![로그인 manager@contoso.com를 보여 주는 스크린샷](secure-data/_static/manager1.png)

<span data-ttu-id="e7ef4-319">다음 이미지는 관리자의 연락처 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-319">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자의 보기](secure-data/_static/manager.png)

<span data-ttu-id="e7ef4-321">**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="e7ef4-322">다음 그림에서 `admin@contoso.com`는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![로그인 admin@contoso.com를 보여 주는 스크린샷](secure-data/_static/admin.png)

<span data-ttu-id="e7ef4-324">관리자는 모든 권한을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-324">The administrator has all privileges.</span></span> <span data-ttu-id="e7ef4-325">그녀는 연락처 읽기/편집/삭제할 수 및 연락처의 상태를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="e7ef4-326">앱은 다음 `Contact` 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="e7ef4-327">다음 인증 처리기를 포함 하는 샘플:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="e7ef4-328">`ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="e7ef4-329">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="e7ef4-330">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e7ef4-331">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="e7ef4-331">Prerequisites</span></span>

<span data-ttu-id="e7ef4-332">이 자습서 고급 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-332">This tutorial is advanced.</span></span> <span data-ttu-id="e7ef4-333">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-333">You should be familiar with:</span></span>

* [<span data-ttu-id="e7ef4-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ef4-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="e7ef4-335">인증</span><span class="sxs-lookup"><span data-stu-id="e7ef4-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="e7ef4-336">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="e7ef4-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="e7ef4-337">권한 부여</span><span class="sxs-lookup"><span data-stu-id="e7ef4-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="e7ef4-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e7ef4-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="e7ef4-339">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="e7ef4-339">The starter and completed app</span></span>

<span data-ttu-id="e7ef4-340">[완성](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="e7ef4-341">보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="e7ef4-342">시작 앱</span><span class="sxs-lookup"><span data-stu-id="e7ef4-342">The starter app</span></span>

<span data-ttu-id="e7ef4-343">[시작](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="e7ef4-344">앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="e7ef4-345">사용자 데이터 보호</span><span class="sxs-lookup"><span data-stu-id="e7ef4-345">Secure user data</span></span>

<span data-ttu-id="e7ef4-346">다음 섹션에서는 모든 주요 단계가 보안 사용자 데이터 앱 만들기 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="e7ef4-347">완료 된 프로젝트 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="e7ef4-348">사용자에 게 연락 데이터를 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-348">Tie the contact data to the user</span></span>

<span data-ttu-id="e7ef4-349">ASP.NET [id](xref:security/authentication/identity) 사용자 id를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="e7ef4-350">`Contact` 모델에 `OwnerID` 및 `ContactStatus`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="e7ef4-351">`OwnerID`는 [id](xref:security/authentication/identity) 데이터베이스의 `AspNetUser` 테이블에서 가져온 사용자의 id입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="e7ef4-352">`Status` 필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="e7ef4-353">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="e7ef4-354">Id에 역할 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="e7ef4-354">Add Role services to Identity</span></span>

<span data-ttu-id="e7ef4-355">역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="e7ef4-356">인증 된 사용자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-356">Require authenticated users</span></span>

<span data-ttu-id="e7ef4-357">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="e7ef4-358">`[AllowAnonymous]` 특성을 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃 (opt out) 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="e7ef4-359">새로 추가 된 Razor 페이지 및 컨트롤러를 보호 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="e7ef4-360">기본적으로 인증을 요구 하는 것은 새 컨트롤러에 의존 하는 것 보다 더 안전 하며, Razor Pages `[Authorize]` 특성을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="e7ef4-361">익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스, 정보 및 연락처 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="e7ef4-362">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="e7ef4-362">Configure the test account</span></span>

<span data-ttu-id="e7ef4-363">`SeedData` 클래스는 관리자와 관리자 라는 두 개의 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="e7ef4-364">암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="e7ef4-365">프로젝트 디렉터리 ( *Program.cs*가 포함 된 디렉터리)에서 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="e7ef4-366">강력한 암호를 지정 하지 않으면 `SeedData.Initialize`를 호출할 때 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="e7ef4-367">테스트 암호를 사용 하도록 `Main`를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="e7ef4-368">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="e7ef4-369">`SeedData` 클래스에서 `Initialize` 메서드를 업데이트 하 여 테스트 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="e7ef4-370">관리자 사용자 ID를 추가 하 고 연락처에 `ContactStatus` 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="e7ef4-371">"제출" 및 "Rejected" 하나의 연락처 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="e7ef4-372">사용자 ID 및 상태를 모든 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="e7ef4-373">하나만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="e7ef4-374">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="e7ef4-375">*권한 부여* 폴더를 만들고 여기에 `ContactIsOwnerAuthorizationHandler` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="e7ef4-376">`ContactIsOwnerAuthorizationHandler`는 리소스에서 사용 하는 사용자가 리소스를 소유 하 고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="e7ef4-377">`ContactIsOwnerAuthorizationHandler` 컨텍스트를 호출 합니다 [. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="e7ef4-378">권한 부여 처리기 일반적으로:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="e7ef4-379">요구 사항이 충족 되 면 `context.Succeed`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="e7ef4-380">요구 사항이 충족 되지 않은 경우 `Task.CompletedTask`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="e7ef4-381">`Task.CompletedTask`는 성공 또는 실패&mdash;다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="e7ef4-382">명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="e7ef4-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="e7ef4-383">앱 편집/삭제/만들려면 연락처 소유자가 자신의 데이터를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="e7ef4-384">`ContactIsOwnerAuthorizationHandler`는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="e7ef4-385">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-385">Create a manager authorization handler</span></span>

<span data-ttu-id="e7ef4-386">*권한 부여* 폴더에 `ContactManagerAuthorizationHandler` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7ef4-387">`ContactManagerAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="e7ef4-388">관리자만 승인 하거나 (새롭거나 변경 된) 콘텐츠 변경 내용을 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="e7ef4-389">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="e7ef4-390">*권한 부여* 폴더에 `ContactAdministratorsAuthorizationHandler` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="e7ef4-391">`ContactAdministratorsAuthorizationHandler`은 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="e7ef4-392">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="e7ef4-393">인증 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-393">Register the authorization handlers</span></span>

<span data-ttu-id="e7ef4-394">[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="e7ef4-395">`ContactIsOwnerAuthorizationHandler`은 Entity Framework Core을 기반으로 하는 ASP.NET Core [id](xref:security/authentication/identity)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="e7ef4-396">[종속성 주입](xref:fundamentals/dependency-injection)을 통해 `ContactsController`에서 사용할 수 있도록 서비스 컬렉션에 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e7ef4-397">`ConfigureServices`의 끝에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="e7ef4-398">`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="e7ef4-399">단일 항목는 EF를 사용 하지 않고 필요한 모든 정보는 `HandleRequirementAsync` 메서드의 `Context` 매개 변수에 있기 때문에 더 이상 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="e7ef4-400">권한 부여를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-400">Support authorization</span></span>

<span data-ttu-id="e7ef4-401">이 섹션에서는 Razor 페이지를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="e7ef4-402">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="e7ef4-403">`ContactOperations` 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="e7ef4-404">이 클래스는 포함 요구 사항을 지 원하는:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="e7ef4-405">연락처 Razor 페이지에 대 한 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="e7ef4-406">연락처 Razor 페이지에에서 사용 된 서비스를 포함 하는 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="e7ef4-407">초기화 코드를 한 위치에 배치 하는 기본 클래스:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="e7ef4-408">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-408">The preceding code:</span></span>

* <span data-ttu-id="e7ef4-409">권한 부여 처리기에 액세스 하는 `IAuthorizationService` 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="e7ef4-410">Id `UserManager` 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="e7ef4-411">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="e7ef4-412">업데이트 된 CreateModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-412">Update the CreateModel</span></span>

<span data-ttu-id="e7ef4-413">`DI_BasePageModel` 기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="e7ef4-414">`CreateModel.OnPostAsync` 메서드를 다음으로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="e7ef4-415">사용자 ID를 `Contact` 모델에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="e7ef4-416">사용자에 게 연락처를 만들 수 있는 권한을 확인 하는 권한 부여 처리기를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="e7ef4-417">업데이트 된 IndexModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-417">Update the IndexModel</span></span>

<span data-ttu-id="e7ef4-418">승인 된 연락처만 일반 사용자에 게 표시 되도록 `OnGetAsync` 메서드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="e7ef4-419">업데이트 된 EditModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-419">Update the EditModel</span></span>

<span data-ttu-id="e7ef4-420">사용자가 연락처를 소유를 확인 하는 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="e7ef4-421">리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="e7ef4-422">특성을 평가 하는 경우 앱 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="e7ef4-423">명령적 리소스 기반 권한 부여를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="e7ef4-424">페이지 모델에 로드 하 여 또는 자체 처리기 내에서 로드 하 여 앱에 리소스에 대 한 액세스 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="e7ef4-425">자주 리소스 키를 전달 하 여 리소스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="e7ef4-426">업데이트 된 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="e7ef4-426">Update the DeleteModel</span></span>

<span data-ttu-id="e7ef4-427">인증 처리기를 사용 하 여 사용자 연락처에 대 한 삭제 권한을 확인 하려면 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="e7ef4-428">권한 부여 서비스 보기 삽입</span><span class="sxs-lookup"><span data-stu-id="e7ef4-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="e7ef4-429">현재 UI은 편집 및 사용자가 수정할 수 없습니다. 연락처에 대 한 링크를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="e7ef4-430">모든 보기에서 사용할 수 있도록 *views/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="e7ef4-431">위의 태그는 여러 개의 `using` 문을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="e7ef4-432">적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e7ef4-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="e7ef4-433">데이터를 변경할 수 있는 권한이 없는 사용자에 게 링크를 숨기기는 앱을 보호 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="e7ef4-434">링크 숨기기는 올바른 링크를 표시 하 여 앱 보다 친숙 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="e7ef4-435">사용자가 편집을 호출 하 고 자신이 소유 하지 않는 데이터에 대 한 작업을 삭제 하도록 생성 된 Url hack 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="e7ef4-436">Razor 페이지 또는 컨트롤러에 데이터를 보호 하는 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="e7ef4-437">세부 정보 업데이트</span><span class="sxs-lookup"><span data-stu-id="e7ef4-437">Update Details</span></span>

<span data-ttu-id="e7ef4-438">관리자 승인 또는 연락처를 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="e7ef4-439">업데이트 세부 정보 페이지 모델:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="e7ef4-440">추가 하거나 역할에 사용자를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-440">Add or remove a user to a role</span></span>

<span data-ttu-id="e7ef4-441">에 대 한 자세한 내용은 다음 [문제](https://github.com/dotnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="e7ef4-442">사용자의 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-442">Removing privileges from a user.</span></span> <span data-ttu-id="e7ef4-443">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="e7ef4-444">사용자에 권한을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="e7ef4-445">완성된 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="e7ef4-445">Test the completed app</span></span>

<span data-ttu-id="e7ef4-446">시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="e7ef4-447">강력한 암호를 선택 합니다: 사용 하 여 8 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="e7ef4-448">예를 들어 `Passw0rd!`은 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="e7ef4-449">프로젝트의 폴더에서 다음 명령을 실행 합니다. 여기서 `<PW>`는 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="e7ef4-450">데이터베이스 삭제 및 업데이트</span><span class="sxs-lookup"><span data-stu-id="e7ef4-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="e7ef4-451">데이터베이스를 시드하려면 앱을 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="e7ef4-452">완성된 된 앱을 테스트 하는 간편한 방법은 3 개의 서로 다른 브라우저 (또는 incognito/InPrivate 세션)을 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="e7ef4-453">한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="e7ef4-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="e7ef4-454">다른 사용자와 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="e7ef4-455">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-455">Verify the following operations:</span></span>

* <span data-ttu-id="e7ef4-456">등록 된 사용자의 모든 승인 된 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="e7ef4-457">등록 된 사용자 편집/삭제할 수 있습니다 자신의 고유 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="e7ef4-458">관리자 수 승인/거부할 연락처 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="e7ef4-459">`Details` 보기에는 **승인** 및 **거부** 단추가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="e7ef4-460">관리자 승인/거부를 편집/삭제할 모든 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="e7ef4-461">사용자</span><span class="sxs-lookup"><span data-stu-id="e7ef4-461">User</span></span>                | <span data-ttu-id="e7ef4-462">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="e7ef4-462">Seeded by the app</span></span> | <span data-ttu-id="e7ef4-463">옵션</span><span class="sxs-lookup"><span data-stu-id="e7ef4-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="e7ef4-464">예</span><span class="sxs-lookup"><span data-stu-id="e7ef4-464">No</span></span>                | <span data-ttu-id="e7ef4-465">자체 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="e7ef4-466">yes</span><span class="sxs-lookup"><span data-stu-id="e7ef4-466">Yes</span></span>               | <span data-ttu-id="e7ef4-467">승인/거부 하 고 편집/삭제할 데이터를 소유 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="e7ef4-468">yes</span><span class="sxs-lookup"><span data-stu-id="e7ef4-468">Yes</span></span>               | <span data-ttu-id="e7ef4-469">승인/거부 하 고 모든 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="e7ef4-470">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="e7ef4-471">삭제에 대 한 URL을 복사 하 고 관리자 연락처에서 편집 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="e7ef4-472">테스트 사용자를 이러한 작업을 수행할 수 없습니다 확인 하려면 테스트 사용자의 브라우저에 이러한 링크를 붙여 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="e7ef4-473">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-473">Create the starter app</span></span>

* <span data-ttu-id="e7ef4-474">"ContactManager" 라는 Razor 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e7ef4-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="e7ef4-475">**개별 사용자 계정을**사용 하 여 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="e7ef4-476">네임 스페이스에는 샘플에 사용 된 네임 스페이스와 일치 하므로 "ContactManager" 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="e7ef4-477">`-uld`는 SQLite 대신 LocalDB를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="e7ef4-478">*모델/연락처를 추가 합니다. cs*:</span><span class="sxs-lookup"><span data-stu-id="e7ef4-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="e7ef4-479">`Contact` 모델을 스 캐 폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="e7ef4-480">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="e7ef4-481">*Pages/_Layout. cshtml* 파일에서 지 각 **관리자** 앵커를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="e7ef4-482">만들기, 편집 및 연락처를 삭제 하 여 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="e7ef4-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="e7ef4-483">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="e7ef4-483">Seed the database</span></span>

<span data-ttu-id="e7ef4-484">*데이터* 폴더에 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="e7ef4-485">`Main`에서 `SeedData.Initialize`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="e7ef4-486">응용 프로그램 데이터베이스를 시드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-486">Test that the app seeded the database.</span></span> <span data-ttu-id="e7ef4-487">DB 연락처의 모든 행이 없으면 시드 메서드가 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="e7ef4-488">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e7ef4-488">Additional resources</span></span>

* [<span data-ttu-id="e7ef4-489">Azure App Service에서 .NET Core 및 SQL Database 웹 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="e7ef4-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="e7ef4-490">[권한 부여 랩을 ASP.NET Core](https://github.com/blowdart/AspNetAuthorizationWorkshop)합니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="e7ef4-491">이 랩에서이 자습서에 도입 된 보안 기능에 자세한 내용으로 이어집니다.</span><span class="sxs-lookup"><span data-stu-id="e7ef4-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="e7ef4-492">사용자 지정 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="e7ef4-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
