---
title: ASP.NET Core 프로젝트의 스 캐 폴드 Id
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Id를 스 캐 폴드 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: b3e077aeac11e62d9e992884100476f7be35b59a
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972039"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="6542e-103">ASP.NET Core 프로젝트의 스 캐 폴드 Id</span><span class="sxs-lookup"><span data-stu-id="6542e-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="6542e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6542e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6542e-105">ASP.NET Core는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6542e-106">Id를 포함 하는 응용 프로그램은 스 캐 폴더를 적용 하 여 RCL (Identity Razor 클래스 라이브러리)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="6542e-107">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="6542e-108">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="6542e-109">생성된 코드는 ID RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="6542e-110">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="6542e-111">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 Rcl id 패키지를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="6542e-112">선택한 ID 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="6542e-113">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="6542e-114">이 문서에서는 Id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="6542e-115">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="6542e-116">Id 스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="6542e-117">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 id를 사용 하는 기타 보안 기능을 사용 하는 경우에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="6542e-118">Id를 스 캐 폴딩을 사용할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="6542e-119">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="6542e-120">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="6542e-121">이 문서에는 스 캐 폴더를 실행할 때 생성 되는 *ScaffoldingReadme* 파일 보다 전체 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-121">This document contains more complete instructions than the *ScaffoldingReadme.txt* file which is generated when running the scaffolder.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="6542e-122">빈 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-122">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6542e-123">다음과 유사한 코드를 사용 하 여 `Startup` 클래스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-123">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="6542e-124">기존 권한 부여 없이 Razor 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-124">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6542e-125">Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-125">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6542e-126">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-126">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="6542e-127">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="6542e-127">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="6542e-128">인증 사용</span><span class="sxs-lookup"><span data-stu-id="6542e-128">Enable authentication</span></span>

<span data-ttu-id="6542e-129">다음과 유사한 코드를 사용 하 여 `Startup` 클래스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-129">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="6542e-130">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="6542e-130">Layout changes</span></span>

<span data-ttu-id="6542e-131">선택 사항: 로그인 부분 (`_LoginPartial`)을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-131">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="6542e-132">권한 부여가 있는 Razor 프로젝트로 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-132">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="6542e-133">일부 Id 옵션은 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-133">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6542e-134">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-134">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="6542e-135">기존 권한 부여 없이 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-135">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6542e-136">선택 사항: *Views/Shared/_Layout* 파일에 로그인 부분 (`_LoginPartial`)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-136">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="6542e-137">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="6542e-137">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="6542e-138">Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-138">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6542e-139">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-139">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="6542e-140">다음과 유사한 코드를 사용 하 여 `Startup` 클래스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-140">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="6542e-141">권한 부여를 사용 하 여 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-141">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="6542e-142">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="6542e-142">Create full identity UI source</span></span>

<span data-ttu-id="6542e-143">Id UI에 대 한 모든 권한을 유지 하려면 Id 스 캐 폴더를 실행 하 고 **모든 파일 재정의**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-143">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="6542e-144">다음 강조 표시 된 코드는 ASP.NET Core 2.1 웹 앱에서 기본 Id UI를 Id로 바꾸는 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-144">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="6542e-145">이 작업을 수행 하 여 Id UI에 대 한 모든 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-145">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="6542e-146">기본 Id는 다음 코드에서 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-146">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="6542e-147">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-147">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="6542e-148">`IEmailSender` 구현을 등록 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-148">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="6542e-149">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="6542e-149">Disable register page</span></span>

<span data-ttu-id="6542e-150">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="6542e-150">To disable user registration:</span></span>

* <span data-ttu-id="6542e-151">스 캐 폴드 Id입니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-151">Scaffold Identity.</span></span> <span data-ttu-id="6542e-152">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-152">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="6542e-153">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="6542e-153">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="6542e-154">사용자가이 끝점에서 등록할 수 없도록 *영역/i d/페이지/계정/등록을 업데이트 합니다* .</span><span class="sxs-lookup"><span data-stu-id="6542e-154">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="6542e-155">이전 변경 내용과 일치 하도록 *영역/i d/페이지/계정*</span><span class="sxs-lookup"><span data-stu-id="6542e-155">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="6542e-156">*영역/i d/페이지/계정/로그인* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-156">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="6542e-157">*영역/i d/페이지/계정/* a p 확인 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-157">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="6542e-158">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-158">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="6542e-159">`PageModel`에서 확인 코드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-159">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="6542e-160">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="6542e-160">Use another app to add users</span></span>

<span data-ttu-id="6542e-161">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-161">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="6542e-162">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-162">Options to add users include:</span></span>

* <span data-ttu-id="6542e-163">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-163">A dedicated admin web app.</span></span>
* <span data-ttu-id="6542e-164">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-164">A console app.</span></span>

<span data-ttu-id="6542e-165">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-165">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="6542e-166">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-166">A list of users is read into memory.</span></span>
* <span data-ttu-id="6542e-167">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-167">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="6542e-168">사용자가 Id 데이터베이스에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-168">The user is added to the Identity database.</span></span>
* <span data-ttu-id="6542e-169">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-169">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="6542e-170">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-170">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="6542e-171">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-171">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="6542e-172">정적 Id 자산 게시 방지</span><span class="sxs-lookup"><span data-stu-id="6542e-172">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="6542e-173">정적 ID 자산을 웹 루트에 게시하지 않으려면 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-173">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6542e-174">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6542e-174">Additional resources</span></span>

* [<span data-ttu-id="6542e-175">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="6542e-175">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6542e-176">ASP.NET Core 2.1 이상에서는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-176">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6542e-177">Id를 포함 하는 응용 프로그램은 스 캐 폴더를 적용 하 여 RCL (Identity Razor 클래스 라이브러리)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-177">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="6542e-178">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-178">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="6542e-179">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-179">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="6542e-180">생성된 코드는 ID RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-180">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="6542e-181">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-181">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="6542e-182">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 Rcl id 패키지를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-182">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="6542e-183">선택한 ID 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-183">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="6542e-184">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-184">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="6542e-185">이 문서에서는 Id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-185">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="6542e-186">Id 스 캐 폴더를 실행 하면 프로젝트 디렉터리에 *ScaffoldingReadme* 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-186">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="6542e-187">*ScaffoldingReadme* 파일에는 id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 사항에 대 한 일반적인 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-187">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="6542e-188">이 문서에는 *ScaffoldingReadme* 파일 보다 더 완전 한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-188">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="6542e-189">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-189">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="6542e-190">Id 스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-190">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="6542e-191">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 id를 사용 하는 기타 보안 기능을 사용 하는 경우에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-191">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="6542e-192">Id를 스 캐 폴딩을 사용할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-192">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="6542e-193">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-193">Services to enable these features must be added manually.</span></span> <span data-ttu-id="6542e-194">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-194">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="6542e-195">빈 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-195">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6542e-196">`Startup` 클래스에 다음의 강조 표시 된 호출을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-196">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="6542e-197">기존 권한 부여 없이 Razor 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-197">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6542e-198">Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-198">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6542e-199">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-199">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="6542e-200">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="6542e-200">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="6542e-201">인증 사용</span><span class="sxs-lookup"><span data-stu-id="6542e-201">Enable authentication</span></span>

<span data-ttu-id="6542e-202">`Startup` 클래스의 `Configure` 메서드에서 `UseStaticFiles`후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-202">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="6542e-203">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="6542e-203">Layout changes</span></span>

<span data-ttu-id="6542e-204">선택 사항: 로그인 부분 (`_LoginPartial`)을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-204">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="6542e-205">권한 부여가 있는 Razor 프로젝트로 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-205">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="6542e-206">일부 Id 옵션은 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-206">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6542e-207">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-207">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="6542e-208">기존 권한 부여 없이 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-208">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="6542e-209">선택 사항: *Views/Shared/_Layout* 파일에 로그인 부분 (`_LoginPartial`)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-209">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="6542e-210">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="6542e-210">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="6542e-211">Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-211">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="6542e-212">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6542e-212">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="6542e-213">`UseStaticFiles`후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-213">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="6542e-214">권한 부여를 사용 하 여 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="6542e-214">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="6542e-215">*Pages/Shared* 폴더와 해당 폴더의 파일을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-215">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="6542e-216">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="6542e-216">Create full identity UI source</span></span>

<span data-ttu-id="6542e-217">Id UI에 대 한 모든 권한을 유지 하려면 Id 스 캐 폴더를 실행 하 고 **모든 파일 재정의**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-217">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="6542e-218">다음 강조 표시 된 코드는 ASP.NET Core 2.1 웹 앱에서 기본 Id UI를 Id로 바꾸는 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-218">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="6542e-219">이 작업을 수행 하 여 Id UI에 대 한 모든 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-219">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="6542e-220">기본 Id는 다음 코드에서 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-220">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="6542e-221">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-221">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="6542e-222">`IEmailSender` 구현을 등록 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-222">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="6542e-223">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="6542e-223">Disable register page</span></span>

<span data-ttu-id="6542e-224">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="6542e-224">To disable user registration:</span></span>

* <span data-ttu-id="6542e-225">스 캐 폴드 Id입니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-225">Scaffold Identity.</span></span> <span data-ttu-id="6542e-226">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-226">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="6542e-227">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="6542e-227">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="6542e-228">사용자가이 끝점에서 등록할 수 없도록 *영역/i d/페이지/계정/등록을 업데이트 합니다* .</span><span class="sxs-lookup"><span data-stu-id="6542e-228">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="6542e-229">이전 변경 내용과 일치 하도록 *영역/i d/페이지/계정*</span><span class="sxs-lookup"><span data-stu-id="6542e-229">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="6542e-230">*영역/i d/페이지/계정/로그인* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-230">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="6542e-231">*영역/i d/페이지/계정/* a p 확인 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-231">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="6542e-232">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-232">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="6542e-233">`PageModel`에서 확인 코드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-233">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="6542e-234">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="6542e-234">Use another app to add users</span></span>

<span data-ttu-id="6542e-235">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-235">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="6542e-236">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-236">Options to add users include:</span></span>

* <span data-ttu-id="6542e-237">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-237">A dedicated admin web app.</span></span>
* <span data-ttu-id="6542e-238">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-238">A console app.</span></span>

<span data-ttu-id="6542e-239">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-239">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="6542e-240">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-240">A list of users is read into memory.</span></span>
* <span data-ttu-id="6542e-241">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-241">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="6542e-242">사용자가 Id 데이터베이스에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-242">The user is added to the Identity database.</span></span>
* <span data-ttu-id="6542e-243">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-243">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="6542e-244">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-244">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="6542e-245">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6542e-245">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6542e-246">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6542e-246">Additional resources</span></span>

* [<span data-ttu-id="6542e-247">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="6542e-247">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end