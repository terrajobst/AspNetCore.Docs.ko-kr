---
title: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기
author: rick-anderson
description: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 Razor 페이지 앱을 만드는 방법에 알아봅니다. HTTPS, 인증, 보안, ASP.NET Core Id를 포함합니다.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 225d0e3aa51745253d03e614b1c8568b3a6ba731
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017489"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="5d656-104">권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="5d656-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="5d656-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="5d656-106">참조 [이 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core MVC 버전에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="5d656-107">이 자습서에서는 ASP.NET Core 1.1 버전은 [이](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="5d656-108">ASP.NET Core 샘플에는 1.1 합니다 [샘플](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5d656-109">참조 [이 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="5d656-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d656-110">이 자습서에는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="5d656-111">인증 된 (등록 된) 사용자는 연락처 목록에 표시를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="5d656-112">세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-112">There are three security groups:</span></span>

* <span data-ttu-id="5d656-113">**등록 된 사용자** 편집/삭제할 수 있습니다 자신의 데이터 및 승인 된 모든 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="5d656-114">**관리자** 승인 하거나 연락처 데이터를 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="5d656-115">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="5d656-116">**관리자** 승인/거부를 편집/삭제할 모든 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="5d656-117">이 문서의 이미지는 최신 템플릿과 정확히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="5d656-118">다음 이미지에서는 Rick 사용자 (`rick@example.com`)에 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="5d656-119">Rick 승인 된 연락처를 보기만 할 수 있습니다 하 고 **편집할**/**삭제**/**새로 만들기** 자신의 연락처에 대 한 링크입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="5d656-120">Rick을 표시 하 여 만든 마지막 레코드만 **편집** 하 고 **삭제** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="5d656-121">다른 사용자에 게는 관리자가 "승인 됨" 상태 변경 될 때까지 마지막 레코드를 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![로그인 한 Rick을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="5d656-123">다음 이미지 `manager@contoso.com` 에서는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![보여 주는 스크린샷 manager@contoso.com 로그인](secure-data/_static/manager1.png)

<span data-ttu-id="5d656-125">다음 이미지는 관리자의 연락처 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-125">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자의 보기](secure-data/_static/manager.png)

<span data-ttu-id="5d656-127">합니다 **승인** 하 고 **거부** 단추가 관리자와 관리자만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="5d656-128">다음 이미지 `admin@contoso.com` 에서는 및 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![보여 주는 스크린샷 admin@contoso.com 로그인](secure-data/_static/admin.png)

<span data-ttu-id="5d656-130">관리자는 모든 권한을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-130">The administrator has all privileges.</span></span> <span data-ttu-id="5d656-131">그녀는 연락처 읽기/편집/삭제할 수 및 연락처의 상태를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="5d656-132">앱에서 만든 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 다음 `Contact` 모델:</span><span class="sxs-lookup"><span data-stu-id="5d656-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="5d656-133">다음 인증 처리기를 포함 하는 샘플:</span><span class="sxs-lookup"><span data-stu-id="5d656-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="5d656-134">`ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="5d656-135">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="5d656-136">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5d656-137">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="5d656-137">Prerequisites</span></span>

<span data-ttu-id="5d656-138">이 자습서 고급 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-138">This tutorial is advanced.</span></span> <span data-ttu-id="5d656-139">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-139">You should be familiar with:</span></span>

* [<span data-ttu-id="5d656-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5d656-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="5d656-141">인증</span><span class="sxs-lookup"><span data-stu-id="5d656-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5d656-142">계정 확인 및 비밀번호 복구</span><span class="sxs-lookup"><span data-stu-id="5d656-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="5d656-143">권한 부여</span><span class="sxs-lookup"><span data-stu-id="5d656-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="5d656-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5d656-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="5d656-145">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="5d656-145">The starter and completed app</span></span>

<span data-ttu-id="5d656-146">[다운로드](xref:index#how-to-download-a-sample) 는 [완료](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 앱.</span><span class="sxs-lookup"><span data-stu-id="5d656-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="5d656-147">[테스트](#test-the-completed-app) 완성된 된 앱의 보안 기능에 익숙해질 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="5d656-148">시작 앱</span><span class="sxs-lookup"><span data-stu-id="5d656-148">The starter app</span></span>

<span data-ttu-id="5d656-149">[다운로드](xref:index#how-to-download-a-sample) 는 [스타터](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱.</span><span class="sxs-lookup"><span data-stu-id="5d656-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="5d656-150">앱 실행을 탭 합니다 **ContactManager** 에 연결 하 고 만들기, 편집 및 연락처를 삭제를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="5d656-151">사용자 데이터 보호</span><span class="sxs-lookup"><span data-stu-id="5d656-151">Secure user data</span></span>

<span data-ttu-id="5d656-152">다음 섹션에서는 모든 주요 단계가 보안 사용자 데이터 앱 만들기 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="5d656-153">완료 된 프로젝트 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="5d656-154">사용자에 게 연락 데이터를 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-154">Tie the contact data to the user</span></span>

<span data-ttu-id="5d656-155">ASP.NET을 사용 하 여 [Identity](xref:security/authentication/identity) 데이터에 있지만 다른 사용자 데이터가 아닌 사용자 ID 사용자를 편집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="5d656-156">추가 `OwnerID` 하 고 `ContactStatus` 에 `Contact` 모델:</span><span class="sxs-lookup"><span data-stu-id="5d656-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="5d656-157">`OwnerID` 사용자의 id를 `AspNetUser` 테이블에 [Identity](xref:security/authentication/identity) 데이터베이스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="5d656-158">`Status` 필드 연락처를 일반 사용자가 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="5d656-159">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-159">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="5d656-160">Id에 역할 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="5d656-160">Add Role services to Identity</span></span>

<span data-ttu-id="5d656-161">추가 [역할](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 역할 서비스를 추가 하려면:</span><span class="sxs-lookup"><span data-stu-id="5d656-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="5d656-162">인증 된 사용자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-162">Require authenticated users</span></span>

<span data-ttu-id="5d656-163">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="5d656-164">사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃할 수 있습니다는 `[AllowAnonymous]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="5d656-165">새로 추가 된 Razor 페이지 및 컨트롤러를 보호 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="5d656-166">기본적으로 필요한 인증은 새 컨트롤러 및 포함할 Razor 페이지에 의존 하는 것 보다 안전 하지는 `[Authorize]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="5d656-167">익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스 및 개인 정보 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="5d656-168">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="5d656-168">Configure the test account</span></span>

<span data-ttu-id="5d656-169">`SeedData` 클래스에는 두 개의 계정을 만듭니다: 관리자 및 관리자입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="5d656-170">사용 된 [암호 관리자 도구](xref:security/app-secrets) 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="5d656-171">프로젝트 디렉터리에서 암호를 설정 (포함 하는 디렉터리 *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="5d656-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="5d656-172">강력한 암호를 지정 하지 않으면 예외가 발생 될 때 `SeedData.Initialize` 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="5d656-173">업데이트 `Main` 테스트 암호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="5d656-174">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="5d656-175">업데이트를 `Initialize` 의 메서드는 `SeedData` 클래스 테스트 계정을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="5d656-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="5d656-176">관리자 사용자 ID를 추가 하 고 `ContactStatus` 연락처를 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="5d656-177">"제출" 및 "Rejected" 하나의 연락처 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="5d656-178">사용자 ID 및 상태를 모든 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="5d656-179">하나만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="5d656-180">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="5d656-181">만들기는 `ContactIsOwnerAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5d656-182">`ContactIsOwnerAuthorizationHandler` 리소스에 역할을 하는 사용자 리소스를 소유 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="5d656-183">합니다 `ContactIsOwnerAuthorizationHandler` 호출 [컨텍스트. 성공](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 현재 인증 된 사용자가 연락처 소유자입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="5d656-184">권한 부여 처리기 일반적으로:</span><span class="sxs-lookup"><span data-stu-id="5d656-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="5d656-185">반환 `context.Succeed` 요구 사항이 충족 되는 경우.</span><span class="sxs-lookup"><span data-stu-id="5d656-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="5d656-186">반환 `Task.CompletedTask` 요구 사항이 충족 되지 않는 경우.</span><span class="sxs-lookup"><span data-stu-id="5d656-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="5d656-187">`Task.CompletedTask`은 (는) 성공&mdash;또는 실패 하지 않으므로 다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="5d656-188">명시적으로 실패 하는 경우 반환 [컨텍스트. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="5d656-189">앱 편집/삭제/만들려면 연락처 소유자가 자신의 데이터를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="5d656-190">`ContactIsOwnerAuthorizationHandler` 요구 사항 매개 변수에 전달 된 작업을 확인 하려면 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="5d656-191">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-191">Create a manager authorization handler</span></span>

<span data-ttu-id="5d656-192">만들기는 `ContactManagerAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5d656-193">`ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="5d656-194">관리자만 승인 하거나 (새롭거나 변경 된) 콘텐츠 변경 내용을 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="5d656-195">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="5d656-196">만들기는 `ContactAdministratorsAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5d656-197">`ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="5d656-198">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="5d656-199">인증 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-199">Register the authorization handlers</span></span>

<span data-ttu-id="5d656-200">Entity Framework Core를 사용 하 여 서비스에 등록 해야 합니다 [종속성 주입](xref:fundamentals/dependency-injection) 사용 하 여 [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="5d656-201">합니다 `ContactIsOwnerAuthorizationHandler` ASP.NET Core를 사용 하 여 [Identity](xref:security/authentication/identity), Entity Framework Core에서 빌드되는 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="5d656-202">사용할 수 있도록 서비스 컬렉션을 사용 하 여 처리기를 등록 합니다 `ContactsController` 를 통해 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5d656-203">끝에 다음 코드를 추가 `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5d656-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="5d656-204">`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="5d656-205">EF를 사용 하지 않는 서 필요한 모든 정보는 단일 항목 들은 합니다 `Context` 의 매개 변수는 `HandleRequirementAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="5d656-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="5d656-206">권한 부여를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-206">Support authorization</span></span>

<span data-ttu-id="5d656-207">이 섹션에서는 Razor 페이지를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="5d656-208">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="5d656-209">검토를 `ContactOperations` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="5d656-210">이 클래스는 포함 요구 사항을 지 원하는:</span><span class="sxs-lookup"><span data-stu-id="5d656-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="5d656-211">연락처 Razor 페이지에 대 한 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="5d656-212">연락처 Razor 페이지에에서 사용 된 서비스를 포함 하는 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="5d656-213">초기화 코드를 한 위치에 배치 하는 기본 클래스:</span><span class="sxs-lookup"><span data-stu-id="5d656-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="5d656-214">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="5d656-214">The preceding code:</span></span>

* <span data-ttu-id="5d656-215">추가 된 `IAuthorizationService` 권한 부여 처리기에 액세스 하는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="5d656-216">Id 추가 `UserManager` 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="5d656-217">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="5d656-218">업데이트 된 CreateModel</span><span class="sxs-lookup"><span data-stu-id="5d656-218">Update the CreateModel</span></span>

<span data-ttu-id="5d656-219">만들기 페이지 모델 생성자를 사용 하 여 업데이트를 `DI_BasePageModel` 기본 클래스:</span><span class="sxs-lookup"><span data-stu-id="5d656-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="5d656-220">업데이트 된 `CreateModel.OnPostAsync` 방법:</span><span class="sxs-lookup"><span data-stu-id="5d656-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="5d656-221">사용자 ID를 추가 합니다 `Contact` 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="5d656-222">사용자에 게 연락처를 만들 수 있는 권한을 확인 하는 권한 부여 처리기를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="5d656-223">업데이트 된 IndexModel</span><span class="sxs-lookup"><span data-stu-id="5d656-223">Update the IndexModel</span></span>

<span data-ttu-id="5d656-224">업데이트 된 `OnGetAsync` 메서드 승인 된 연락처만 일반 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="5d656-225">업데이트 된 EditModel</span><span class="sxs-lookup"><span data-stu-id="5d656-225">Update the EditModel</span></span>

<span data-ttu-id="5d656-226">사용자가 연락처를 소유를 확인 하는 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="5d656-227">리소스 권한 부여가 유효성을 검사 하기 때문에 `[Authorize]` 특성 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="5d656-228">특성을 평가 하는 경우 앱 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="5d656-229">명령적 리소스 기반 권한 부여를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="5d656-230">페이지 모델에 로드 하 여 또는 자체 처리기 내에서 로드 하 여 앱에 리소스에 대 한 액세스 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="5d656-231">자주 리소스 키를 전달 하 여 리소스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="5d656-232">업데이트 된 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="5d656-232">Update the DeleteModel</span></span>

<span data-ttu-id="5d656-233">인증 처리기를 사용 하 여 사용자 연락처에 대 한 삭제 권한을 확인 하려면 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="5d656-234">권한 부여 서비스 보기 삽입</span><span class="sxs-lookup"><span data-stu-id="5d656-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="5d656-235">현재 UI은 편집 및 사용자가 수정할 수 없습니다. 연락처에 대 한 링크를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="5d656-236">모든 보기에서 사용할 수 있도록 *Pages/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="5d656-237">위의 태그는 몇 개 추가 `using` 문입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="5d656-238">업데이트를 **편집** 및 **삭제** 에 연결 *Pages/Contacts/Index.cshtml* 하므로 적절 한 권한 가진 사용자만 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="5d656-239">데이터를 변경할 수 있는 권한이 없는 사용자에 게 링크를 숨기기는 앱을 보호 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="5d656-240">링크 숨기기는 올바른 링크를 표시 하 여 앱 보다 친숙 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="5d656-241">사용자가 편집을 호출 하 고 자신이 소유 하지 않는 데이터에 대 한 작업을 삭제 하도록 생성 된 Url hack 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="5d656-242">Razor 페이지 또는 컨트롤러에 데이터를 보호 하는 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="5d656-243">세부 정보 업데이트</span><span class="sxs-lookup"><span data-stu-id="5d656-243">Update Details</span></span>

<span data-ttu-id="5d656-244">관리자 승인 또는 연락처를 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="5d656-245">업데이트 세부 정보 페이지 모델:</span><span class="sxs-lookup"><span data-stu-id="5d656-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="5d656-246">추가 하거나 역할에 사용자를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-246">Add or remove a user to a role</span></span>

<span data-ttu-id="5d656-247">참조 [이 문제](https://github.com/aspnet/AspNetCore.Docs/issues/8502) 에 대 한 내용은:</span><span class="sxs-lookup"><span data-stu-id="5d656-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="5d656-248">사용자의 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-248">Removing privileges from a user.</span></span> <span data-ttu-id="5d656-249">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="5d656-250">사용자에 권한을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-250">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="5d656-251">완성된 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="5d656-251">Test the completed app</span></span>

<span data-ttu-id="5d656-252">시드 된 사용자 계정의 암호를 이미 설정 하지 않은 경우 사용 합니다 [암호 관리자 도구](xref:security/app-secrets#secret-manager) 암호를 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="5d656-252">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="5d656-253">강력한 암호를 선택 합니다. 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-253">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="5d656-254">예를 들어 `Passw0rd!` 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-254">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="5d656-255">프로젝트의 폴더에서 다음 명령을 실행 하는 `<PW>` 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-255">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="5d656-256">앱에 있는 경우 연락처:</span><span class="sxs-lookup"><span data-stu-id="5d656-256">If the app has contacts:</span></span>

* <span data-ttu-id="5d656-257">모든 레코드를 삭제 합니다 `Contact` 테이블입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-257">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="5d656-258">데이터베이스를 시드하려면 앱을 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-258">Restart the app to seed the database.</span></span>

<span data-ttu-id="5d656-259">완성된 된 앱을 테스트 하는 간편한 방법은 3 개의 서로 다른 브라우저 (또는 incognito/InPrivate 세션)을 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-259">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="5d656-260">하나의 브라우저에서 새 사용자를 등록 합니다 (예를 들어 `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="5d656-260">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="5d656-261">다른 사용자와 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-261">Sign in to each browser with a different user.</span></span> <span data-ttu-id="5d656-262">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-262">Verify the following operations:</span></span>

* <span data-ttu-id="5d656-263">등록 된 사용자의 모든 승인 된 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-263">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="5d656-264">등록 된 사용자 편집/삭제할 수 있습니다 자신의 고유 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-264">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="5d656-265">관리자 수 승인/거부할 연락처 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-265">Managers can approve/reject contact data.</span></span> <span data-ttu-id="5d656-266">합니다 `Details` 표시를 볼 **승인** 하 고 **거부** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-266">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="5d656-267">관리자 승인/거부를 편집/삭제할 모든 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-267">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="5d656-268">사용자</span><span class="sxs-lookup"><span data-stu-id="5d656-268">User</span></span>                | <span data-ttu-id="5d656-269">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="5d656-269">Seeded by the app</span></span> | <span data-ttu-id="5d656-270">옵션</span><span class="sxs-lookup"><span data-stu-id="5d656-270">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="5d656-271">아니요</span><span class="sxs-lookup"><span data-stu-id="5d656-271">No</span></span>                | <span data-ttu-id="5d656-272">자체 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-272">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="5d656-273">예</span><span class="sxs-lookup"><span data-stu-id="5d656-273">Yes</span></span>               | <span data-ttu-id="5d656-274">승인/거부 하 고 편집/삭제할 데이터를 소유 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-274">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="5d656-275">예</span><span class="sxs-lookup"><span data-stu-id="5d656-275">Yes</span></span>               | <span data-ttu-id="5d656-276">승인/거부 하 고 모든 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-276">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="5d656-277">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-277">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="5d656-278">삭제에 대 한 URL을 복사 하 고 관리자 연락처에서 편집 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-278">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="5d656-279">테스트 사용자를 이러한 작업을 수행할 수 없습니다 확인 하려면 테스트 사용자의 브라우저에 이러한 링크를 붙여 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-279">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="5d656-280">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-280">Create the starter app</span></span>

* <span data-ttu-id="5d656-281">"ContactManager" 라는 Razor 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-281">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="5d656-282">응용 프로그램을 만들 **개별 사용자 계정**합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-282">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="5d656-283">네임 스페이스에는 샘플에 사용 된 네임 스페이스와 일치 하므로 "ContactManager" 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-283">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="5d656-284">`-uld` SQLite 대신 LocalDB를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-284">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="5d656-285">*모델/연락처를 추가 합니다. cs*:</span><span class="sxs-lookup"><span data-stu-id="5d656-285">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="5d656-286">스 캐 폴드는 `Contact` 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-286">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="5d656-287">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-287">Create initial migration and update the database:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
  ```

<span data-ttu-id="5d656-288">`dotnet aspnet-codegenerator razorpage` 명령을 사용 하 여 버그를 발생 하는 경우 [이 GitHub 문제](https://github.com/aspnet/Scaffolding/issues/984)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5d656-288">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="5d656-289">*Pages/Shared/Layout. cshtml* 파일에서 지 각 **관리자** 앵커를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-289">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="5d656-290">만들기, 편집 및 연락처를 삭제 하 여 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="5d656-290">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="5d656-291">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="5d656-291">Seed the database</span></span>

<span data-ttu-id="5d656-292">*데이터* 폴더에 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-292">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="5d656-293">호출 `SeedData.Initialize` 에서 `Main`:</span><span class="sxs-lookup"><span data-stu-id="5d656-293">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="5d656-294">응용 프로그램 데이터베이스를 시드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-294">Test that the app seeded the database.</span></span> <span data-ttu-id="5d656-295">DB 연락처의 모든 행이 없으면 시드 메서드가 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-295">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="5d656-296">이 자습서에는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-296">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="5d656-297">인증 된 (등록 된) 사용자는 연락처 목록에 표시를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-297">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="5d656-298">세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-298">There are three security groups:</span></span>

* <span data-ttu-id="5d656-299">**등록 된 사용자** 편집/삭제할 수 있습니다 자신의 데이터 및 승인 된 모든 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-299">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="5d656-300">**관리자** 승인 하거나 연락처 데이터를 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-300">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="5d656-301">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-301">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="5d656-302">**관리자** 승인/거부를 편집/삭제할 모든 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-302">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="5d656-303">다음 이미지에서는 Rick 사용자 (`rick@example.com`)에 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-303">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="5d656-304">Rick 승인 된 연락처를 보기만 할 수 있습니다 하 고 **편집할**/**삭제**/**새로 만들기** 자신의 연락처에 대 한 링크입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-304">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="5d656-305">Rick을 표시 하 여 만든 마지막 레코드만 **편집** 하 고 **삭제** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-305">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="5d656-306">다른 사용자에 게는 관리자가 "승인 됨" 상태 변경 될 때까지 마지막 레코드를 표시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-306">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![로그인 한 Rick을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="5d656-308">다음 이미지 `manager@contoso.com` 에서는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-308">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![보여 주는 스크린샷 manager@contoso.com 로그인](secure-data/_static/manager1.png)

<span data-ttu-id="5d656-310">다음 이미지는 관리자의 연락처 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-310">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자의 보기](secure-data/_static/manager.png)

<span data-ttu-id="5d656-312">합니다 **승인** 하 고 **거부** 단추가 관리자와 관리자만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-312">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="5d656-313">다음 이미지 `admin@contoso.com` 에서는 및 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-313">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![보여 주는 스크린샷 admin@contoso.com 로그인](secure-data/_static/admin.png)

<span data-ttu-id="5d656-315">관리자는 모든 권한을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-315">The administrator has all privileges.</span></span> <span data-ttu-id="5d656-316">그녀는 연락처 읽기/편집/삭제할 수 및 연락처의 상태를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-316">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="5d656-317">앱에서 만든 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 다음 `Contact` 모델:</span><span class="sxs-lookup"><span data-stu-id="5d656-317">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="5d656-318">다음 인증 처리기를 포함 하는 샘플:</span><span class="sxs-lookup"><span data-stu-id="5d656-318">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="5d656-319">`ContactIsOwnerAuthorizationHandler`: 사용자가 자신의 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-319">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="5d656-320">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-320">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="5d656-321">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-321">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5d656-322">필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="5d656-322">Prerequisites</span></span>

<span data-ttu-id="5d656-323">이 자습서 고급 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-323">This tutorial is advanced.</span></span> <span data-ttu-id="5d656-324">에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-324">You should be familiar with:</span></span>

* [<span data-ttu-id="5d656-325">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5d656-325">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="5d656-326">인증</span><span class="sxs-lookup"><span data-stu-id="5d656-326">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5d656-327">계정 확인 및 비밀번호 복구</span><span class="sxs-lookup"><span data-stu-id="5d656-327">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="5d656-328">권한 부여</span><span class="sxs-lookup"><span data-stu-id="5d656-328">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="5d656-329">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5d656-329">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="5d656-330">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="5d656-330">The starter and completed app</span></span>

<span data-ttu-id="5d656-331">[다운로드](xref:index#how-to-download-a-sample) 는 [완료](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 앱.</span><span class="sxs-lookup"><span data-stu-id="5d656-331">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="5d656-332">[테스트](#test-the-completed-app) 완성된 된 앱의 보안 기능에 익숙해질 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-332">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="5d656-333">시작 앱</span><span class="sxs-lookup"><span data-stu-id="5d656-333">The starter app</span></span>

<span data-ttu-id="5d656-334">[다운로드](xref:index#how-to-download-a-sample) 는 [스타터](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱.</span><span class="sxs-lookup"><span data-stu-id="5d656-334">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="5d656-335">앱 실행을 탭 합니다 **ContactManager** 에 연결 하 고 만들기, 편집 및 연락처를 삭제를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-335">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="5d656-336">사용자 데이터 보호</span><span class="sxs-lookup"><span data-stu-id="5d656-336">Secure user data</span></span>

<span data-ttu-id="5d656-337">다음 섹션에서는 모든 주요 단계가 보안 사용자 데이터 앱 만들기 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-337">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="5d656-338">완료 된 프로젝트 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-338">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="5d656-339">사용자에 게 연락 데이터를 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-339">Tie the contact data to the user</span></span>

<span data-ttu-id="5d656-340">ASP.NET을 사용 하 여 [Identity](xref:security/authentication/identity) 데이터에 있지만 다른 사용자 데이터가 아닌 사용자 ID 사용자를 편집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-340">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="5d656-341">추가 `OwnerID` 하 고 `ContactStatus` 에 `Contact` 모델:</span><span class="sxs-lookup"><span data-stu-id="5d656-341">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="5d656-342">`OwnerID` 사용자의 id를 `AspNetUser` 테이블에 [Identity](xref:security/authentication/identity) 데이터베이스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-342">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="5d656-343">`Status` 필드 연락처를 일반 사용자가 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-343">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="5d656-344">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-344">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="5d656-345">Id에 역할 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="5d656-345">Add Role services to Identity</span></span>

<span data-ttu-id="5d656-346">추가 [역할](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 역할 서비스를 추가 하려면:</span><span class="sxs-lookup"><span data-stu-id="5d656-346">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="5d656-347">인증 된 사용자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-347">Require authenticated users</span></span>

<span data-ttu-id="5d656-348">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-348">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="5d656-349">사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃할 수 있습니다는 `[AllowAnonymous]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-349">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="5d656-350">새로 추가 된 Razor 페이지 및 컨트롤러를 보호 사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-350">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="5d656-351">기본적으로 필요한 인증은 새 컨트롤러 및 포함할 Razor 페이지에 의존 하는 것 보다 안전 하지는 `[Authorize]` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-351">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="5d656-352">추가 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 인덱스에, 정보 및 연락처 페이지 익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-352">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="5d656-353">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="5d656-353">Configure the test account</span></span>

<span data-ttu-id="5d656-354">`SeedData` 클래스에는 두 개의 계정을 만듭니다: 관리자 및 관리자입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-354">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="5d656-355">사용 된 [암호 관리자 도구](xref:security/app-secrets) 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-355">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="5d656-356">프로젝트 디렉터리에서 암호를 설정 (포함 하는 디렉터리 *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="5d656-356">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="5d656-357">강력한 암호를 지정 하지 않으면 예외가 발생 될 때 `SeedData.Initialize` 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-357">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="5d656-358">업데이트 `Main` 테스트 암호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-358">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="5d656-359">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-359">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="5d656-360">업데이트를 `Initialize` 의 메서드는 `SeedData` 클래스 테스트 계정을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="5d656-360">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="5d656-361">관리자 사용자 ID를 추가 하 고 `ContactStatus` 연락처를 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-361">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="5d656-362">"제출" 및 "Rejected" 하나의 연락처 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-362">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="5d656-363">사용자 ID 및 상태를 모든 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-363">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="5d656-364">하나만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-364">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="5d656-365">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-365">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="5d656-366">만들기는 `ContactIsOwnerAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-366">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5d656-367">`ContactIsOwnerAuthorizationHandler` 리소스에 역할을 하는 사용자 리소스를 소유 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-367">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="5d656-368">합니다 `ContactIsOwnerAuthorizationHandler` 호출 [컨텍스트. 성공](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 현재 인증 된 사용자가 연락처 소유자입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-368">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="5d656-369">권한 부여 처리기 일반적으로:</span><span class="sxs-lookup"><span data-stu-id="5d656-369">Authorization handlers generally:</span></span>

* <span data-ttu-id="5d656-370">반환 `context.Succeed` 요구 사항이 충족 되는 경우.</span><span class="sxs-lookup"><span data-stu-id="5d656-370">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="5d656-371">반환 `Task.CompletedTask` 요구 사항이 충족 되지 않는 경우.</span><span class="sxs-lookup"><span data-stu-id="5d656-371">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="5d656-372">`Task.CompletedTask`은 (는) 성공&mdash;또는 실패 하지 않으므로 다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-372">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="5d656-373">명시적으로 실패 하는 경우 반환 [컨텍스트. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-373">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="5d656-374">앱 편집/삭제/만들려면 연락처 소유자가 자신의 데이터를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-374">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="5d656-375">`ContactIsOwnerAuthorizationHandler` 요구 사항 매개 변수에 전달 된 작업을 확인 하려면 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-375">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="5d656-376">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-376">Create a manager authorization handler</span></span>

<span data-ttu-id="5d656-377">만들기는 `ContactManagerAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-377">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5d656-378">`ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-378">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="5d656-379">관리자만 승인 하거나 (새롭거나 변경 된) 콘텐츠 변경 내용을 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-379">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="5d656-380">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-380">Create an administrator authorization handler</span></span>

<span data-ttu-id="5d656-381">만들기는 `ContactAdministratorsAuthorizationHandler` 클래스를 *권한 부여* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-381">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5d656-382">`ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-382">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="5d656-383">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-383">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="5d656-384">인증 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-384">Register the authorization handlers</span></span>

<span data-ttu-id="5d656-385">Entity Framework Core를 사용 하 여 서비스에 등록 해야 합니다 [종속성 주입](xref:fundamentals/dependency-injection) 사용 하 여 [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-385">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="5d656-386">합니다 `ContactIsOwnerAuthorizationHandler` ASP.NET Core를 사용 하 여 [Identity](xref:security/authentication/identity), Entity Framework Core에서 빌드되는 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-386">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="5d656-387">사용할 수 있도록 서비스 컬렉션을 사용 하 여 처리기를 등록 합니다 `ContactsController` 를 통해 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-387">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5d656-388">끝에 다음 코드를 추가 `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5d656-388">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="5d656-389">`ContactAdministratorsAuthorizationHandler` 및 `ContactManagerAuthorizationHandler` 단일 항목으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-389">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="5d656-390">EF를 사용 하지 않는 서 필요한 모든 정보는 단일 항목 들은 합니다 `Context` 의 매개 변수는 `HandleRequirementAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="5d656-390">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="5d656-391">권한 부여를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-391">Support authorization</span></span>

<span data-ttu-id="5d656-392">이 섹션에서는 Razor 페이지를 업데이트 하 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-392">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="5d656-393">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-393">Review the contact operations requirements class</span></span>

<span data-ttu-id="5d656-394">검토를 `ContactOperations` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-394">Review the `ContactOperations` class.</span></span> <span data-ttu-id="5d656-395">이 클래스는 포함 요구 사항을 지 원하는:</span><span class="sxs-lookup"><span data-stu-id="5d656-395">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="5d656-396">연락처 Razor 페이지에 대 한 기본 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-396">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="5d656-397">연락처 Razor 페이지에에서 사용 된 서비스를 포함 하는 기본 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-397">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="5d656-398">초기화 코드를 한 위치에 배치 하는 기본 클래스:</span><span class="sxs-lookup"><span data-stu-id="5d656-398">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="5d656-399">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="5d656-399">The preceding code:</span></span>

* <span data-ttu-id="5d656-400">추가 된 `IAuthorizationService` 권한 부여 처리기에 액세스 하는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-400">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="5d656-401">Id 추가 `UserManager` 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-401">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="5d656-402">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-402">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="5d656-403">업데이트 된 CreateModel</span><span class="sxs-lookup"><span data-stu-id="5d656-403">Update the CreateModel</span></span>

<span data-ttu-id="5d656-404">만들기 페이지 모델 생성자를 사용 하 여 업데이트를 `DI_BasePageModel` 기본 클래스:</span><span class="sxs-lookup"><span data-stu-id="5d656-404">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="5d656-405">업데이트 된 `CreateModel.OnPostAsync` 방법:</span><span class="sxs-lookup"><span data-stu-id="5d656-405">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="5d656-406">사용자 ID를 추가 합니다 `Contact` 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-406">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="5d656-407">사용자에 게 연락처를 만들 수 있는 권한을 확인 하는 권한 부여 처리기를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-407">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="5d656-408">업데이트 된 IndexModel</span><span class="sxs-lookup"><span data-stu-id="5d656-408">Update the IndexModel</span></span>

<span data-ttu-id="5d656-409">업데이트 된 `OnGetAsync` 메서드 승인 된 연락처만 일반 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-409">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="5d656-410">업데이트 된 EditModel</span><span class="sxs-lookup"><span data-stu-id="5d656-410">Update the EditModel</span></span>

<span data-ttu-id="5d656-411">사용자가 연락처를 소유를 확인 하는 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-411">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="5d656-412">리소스 권한 부여가 유효성을 검사 하기 때문에 `[Authorize]` 특성 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-412">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="5d656-413">특성을 평가 하는 경우 앱 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-413">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="5d656-414">명령적 리소스 기반 권한 부여를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-414">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="5d656-415">페이지 모델에 로드 하 여 또는 자체 처리기 내에서 로드 하 여 앱에 리소스에 대 한 액세스 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-415">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="5d656-416">자주 리소스 키를 전달 하 여 리소스에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-416">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="5d656-417">업데이트 된 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="5d656-417">Update the DeleteModel</span></span>

<span data-ttu-id="5d656-418">인증 처리기를 사용 하 여 사용자 연락처에 대 한 삭제 권한을 확인 하려면 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-418">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="5d656-419">권한 부여 서비스 보기 삽입</span><span class="sxs-lookup"><span data-stu-id="5d656-419">Inject the authorization service into the views</span></span>

<span data-ttu-id="5d656-420">현재 UI은 편집 및 사용자가 수정할 수 없습니다. 연락처에 대 한 링크를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-420">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="5d656-421">권한 부여 서비스를 주입 합니다 *views/_viewimports.cshtml* 모든 보기에 사용할 수 있도록 파일:</span><span class="sxs-lookup"><span data-stu-id="5d656-421">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="5d656-422">위의 태그는 몇 개 추가 `using` 문입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-422">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="5d656-423">업데이트를 **편집** 및 **삭제** 에 연결 *Pages/Contacts/Index.cshtml* 하므로 적절 한 권한 가진 사용자만 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-423">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="5d656-424">데이터를 변경할 수 있는 권한이 없는 사용자에 게 링크를 숨기기는 앱을 보호 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-424">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="5d656-425">링크 숨기기는 올바른 링크를 표시 하 여 앱 보다 친숙 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-425">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="5d656-426">사용자가 편집을 호출 하 고 자신이 소유 하지 않는 데이터에 대 한 작업을 삭제 하도록 생성 된 Url hack 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-426">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="5d656-427">Razor 페이지 또는 컨트롤러에 데이터를 보호 하는 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-427">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="5d656-428">세부 정보 업데이트</span><span class="sxs-lookup"><span data-stu-id="5d656-428">Update Details</span></span>

<span data-ttu-id="5d656-429">관리자 승인 또는 연락처를 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-429">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="5d656-430">업데이트 세부 정보 페이지 모델:</span><span class="sxs-lookup"><span data-stu-id="5d656-430">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="5d656-431">추가 하거나 역할에 사용자를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-431">Add or remove a user to a role</span></span>

<span data-ttu-id="5d656-432">참조 [이 문제](https://github.com/aspnet/AspNetCore.Docs/issues/8502) 에 대 한 내용은:</span><span class="sxs-lookup"><span data-stu-id="5d656-432">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="5d656-433">사용자의 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-433">Removing privileges from a user.</span></span> <span data-ttu-id="5d656-434">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-434">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="5d656-435">사용자에 권한을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-435">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="5d656-436">완성된 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="5d656-436">Test the completed app</span></span>

<span data-ttu-id="5d656-437">시드 된 사용자 계정의 암호를 이미 설정 하지 않은 경우 사용 합니다 [암호 관리자 도구](xref:security/app-secrets#secret-manager) 암호를 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="5d656-437">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="5d656-438">강력한 암호를 선택 합니다. 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-438">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="5d656-439">예를 들어 `Passw0rd!` 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-439">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="5d656-440">프로젝트의 폴더에서 다음 명령을 실행 하는 `<PW>` 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-440">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="5d656-441">데이터베이스 삭제 및 업데이트</span><span class="sxs-lookup"><span data-stu-id="5d656-441">Drop and update the Database</span></span>

    ```console
     dotnet ef database drop -f
     dotnet ef database update  
     ```

* <span data-ttu-id="5d656-442">데이터베이스를 시드하려면 앱을 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-442">Restart the app to seed the database.</span></span>

<span data-ttu-id="5d656-443">완성된 된 앱을 테스트 하는 간편한 방법은 3 개의 서로 다른 브라우저 (또는 incognito/InPrivate 세션)을 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-443">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="5d656-444">하나의 브라우저에서 새 사용자를 등록 합니다 (예를 들어 `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="5d656-444">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="5d656-445">다른 사용자와 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-445">Sign in to each browser with a different user.</span></span> <span data-ttu-id="5d656-446">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-446">Verify the following operations:</span></span>

* <span data-ttu-id="5d656-447">등록 된 사용자의 모든 승인 된 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-447">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="5d656-448">등록 된 사용자 편집/삭제할 수 있습니다 자신의 고유 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-448">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="5d656-449">관리자 수 승인/거부할 연락처 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-449">Managers can approve/reject contact data.</span></span> <span data-ttu-id="5d656-450">합니다 `Details` 표시를 볼 **승인** 하 고 **거부** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-450">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="5d656-451">관리자 승인/거부를 편집/삭제할 모든 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-451">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="5d656-452">사용자</span><span class="sxs-lookup"><span data-stu-id="5d656-452">User</span></span>                | <span data-ttu-id="5d656-453">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="5d656-453">Seeded by the app</span></span> | <span data-ttu-id="5d656-454">옵션</span><span class="sxs-lookup"><span data-stu-id="5d656-454">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="5d656-455">아니요</span><span class="sxs-lookup"><span data-stu-id="5d656-455">No</span></span>                | <span data-ttu-id="5d656-456">자체 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-456">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="5d656-457">예</span><span class="sxs-lookup"><span data-stu-id="5d656-457">Yes</span></span>               | <span data-ttu-id="5d656-458">승인/거부 하 고 편집/삭제할 데이터를 소유 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-458">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="5d656-459">예</span><span class="sxs-lookup"><span data-stu-id="5d656-459">Yes</span></span>               | <span data-ttu-id="5d656-460">승인/거부 하 고 모든 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-460">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="5d656-461">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-461">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="5d656-462">삭제에 대 한 URL을 복사 하 고 관리자 연락처에서 편집 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-462">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="5d656-463">테스트 사용자를 이러한 작업을 수행할 수 없습니다 확인 하려면 테스트 사용자의 브라우저에 이러한 링크를 붙여 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-463">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="5d656-464">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-464">Create the starter app</span></span>

* <span data-ttu-id="5d656-465">"ContactManager" 라는 Razor 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="5d656-465">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="5d656-466">응용 프로그램을 만들 **개별 사용자 계정**합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-466">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="5d656-467">네임 스페이스에는 샘플에 사용 된 네임 스페이스와 일치 하므로 "ContactManager" 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-467">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="5d656-468">`-uld` SQLite 대신 LocalDB를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-468">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="5d656-469">*모델/연락처를 추가 합니다. cs*:</span><span class="sxs-lookup"><span data-stu-id="5d656-469">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="5d656-470">스 캐 폴드는 `Contact` 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-470">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="5d656-471">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-471">Create initial migration and update the database:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="5d656-472">업데이트를 **ContactManager** 에 고정 합니다 *pages/_layout.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="5d656-472">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="5d656-473">만들기, 편집 및 연락처를 삭제 하 여 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="5d656-473">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="5d656-474">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="5d656-474">Seed the database</span></span>

<span data-ttu-id="5d656-475">추가 된 [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 클래스는 *데이터* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-475">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="5d656-476">호출 `SeedData.Initialize` 에서 `Main`:</span><span class="sxs-lookup"><span data-stu-id="5d656-476">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="5d656-477">응용 프로그램 데이터베이스를 시드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-477">Test that the app seeded the database.</span></span> <span data-ttu-id="5d656-478">DB 연락처의 모든 행이 없으면 시드 메서드가 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-478">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="5d656-479">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5d656-479">Additional resources</span></span>

* [<span data-ttu-id="5d656-480">Azure App Service에서.NET Core 및 SQL Database 웹 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="5d656-480">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="5d656-481">[ASP.NET Core 권한 부여 랩](https://github.com/blowdart/AspNetAuthorizationWorkshop)합니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-481">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="5d656-482">이 랩에서이 자습서에 도입 된 보안 기능에 자세한 내용으로 이어집니다.</span><span class="sxs-lookup"><span data-stu-id="5d656-482">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="5d656-483">사용자 지정 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="5d656-483">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
