---
title: ASP.NET Core 프로젝트의 스 캐 폴드 Id
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Id를 스 캐 폴드 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ca2046563281efc3c1cd8f4fec73fe4f8d3fbdda
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816117"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="d9459-103">ASP.NET Core 프로젝트의 스 캐 폴드 Id</span><span class="sxs-lookup"><span data-stu-id="d9459-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="d9459-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d9459-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d9459-105">ASP.NET Core 2.1 이상에서는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d9459-106">Id를 포함 하는 응용 프로그램은 스 캐 폴더를 적용 하 여 RCL (Identity Razor 클래스 라이브러리)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="d9459-107">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="d9459-108">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="d9459-109">생성된 코드는 ID RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="d9459-110">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d9459-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="d9459-111">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 Rcl id 패키지를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="d9459-112">선택한 ID 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="d9459-113">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="d9459-114">이 문서에서는 Id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="d9459-115">Id 스 캐 폴더를 실행 하면 프로젝트 디렉터리에 *ScaffoldingReadme* 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="d9459-116">*ScaffoldingReadme* 파일에는 id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 사항에 대 한 일반적인 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="d9459-117">이 문서에는 *ScaffoldingReadme* 파일 보다 더 완전 한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="d9459-118">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="d9459-119">Id 스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="d9459-120">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 id를 사용 하는 기타 보안 기능을 사용 하는 경우에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="d9459-121">Id를 스 캐 폴딩을 사용할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="d9459-122">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="d9459-123">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d9459-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="d9459-124">빈 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="d9459-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="d9459-125">`Startup` 클래스에 다음의 강조 표시 된 호출을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="d9459-126">기존 권한 부여 없이 Razor 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="d9459-126">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="d9459-127">Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d9459-128">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d9459-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="d9459-129">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="d9459-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="d9459-130">인증 사용</span><span class="sxs-lookup"><span data-stu-id="d9459-130">Enable authentication</span></span>

<span data-ttu-id="d9459-131">`Startup` 클래스의 `Configure` 메서드에서 `UseStaticFiles`후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="d9459-132">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="d9459-132">Layout changes</span></span>

<span data-ttu-id="d9459-133">선택 사항: 로그인 부분 (`_LoginPartial`)을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="d9459-134">권한 부여가 있는 Razor 프로젝트로 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="d9459-134">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="d9459-135">일부 Id 옵션은 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d9459-136">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d9459-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="d9459-137">기존 권한 부여 없이 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="d9459-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="d9459-138">선택 사항: *Views/Shared/Layout. cshtml* 파일에 로그인 부분 (`_LoginPartial`)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="d9459-139">*Pages/shared/_LoginPartial* 파일을 *Views/shared/_LoginPartial* 로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="d9459-140">Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="d9459-141">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d9459-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="d9459-142">`UseStaticFiles`후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="d9459-143">권한 부여를 사용 하 여 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="d9459-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="d9459-144">*Pages/Shared* 폴더와 해당 폴더의 파일을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="d9459-145">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="d9459-145">Create full identity UI source</span></span>

<span data-ttu-id="d9459-146">Id UI에 대 한 모든 권한을 유지 하려면 Id 스 캐 폴더를 실행 하 고 **모든 파일 재정의**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="d9459-147">다음 강조 표시 된 코드는 ASP.NET Core 2.1 웹 앱에서 기본 Id UI를 Id로 바꾸는 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="d9459-148">이 작업을 수행 하 여 Id UI에 대 한 모든 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="d9459-149">기본 Id는 다음 코드에서 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="d9459-150">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="d9459-151">`IEmailSender` 구현을 등록 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="d9459-152">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="d9459-152">Disable register page</span></span>

<span data-ttu-id="d9459-153">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="d9459-153">To disable user registration:</span></span>

* <span data-ttu-id="d9459-154">스 캐 폴드 Id입니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-154">Scaffold Identity.</span></span> <span data-ttu-id="d9459-155">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-155">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="d9459-156">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="d9459-156">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="d9459-157">사용자가이 끝점에서 등록할 수 없도록 *영역/i d/페이지/계정/등록을 업데이트 합니다* .</span><span class="sxs-lookup"><span data-stu-id="d9459-157">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="d9459-158">이전 변경 내용과 일치 하도록 *영역/i d/페이지/계정*</span><span class="sxs-lookup"><span data-stu-id="d9459-158">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="d9459-159">*영역/i d/페이지/계정/로그인* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-159">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="d9459-160">*영역/i d/페이지/계정/* a p 확인 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-160">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="d9459-161">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-161">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="d9459-162">`PageModel`에서 확인 코드를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-162">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="d9459-163">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="d9459-163">Use another app to add users</span></span>

<span data-ttu-id="d9459-164">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-164">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="d9459-165">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-165">Options to add users include:</span></span>

* <span data-ttu-id="d9459-166">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-166">A dedicated admin web app.</span></span>
* <span data-ttu-id="d9459-167">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-167">A console app.</span></span>

<span data-ttu-id="d9459-168">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-168">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="d9459-169">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-169">A list of users is read into memory.</span></span>
* <span data-ttu-id="d9459-170">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-170">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="d9459-171">사용자가 Id 데이터베이스에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-171">The user is added to the Identity database.</span></span>
* <span data-ttu-id="d9459-172">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-172">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="d9459-173">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-173">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="d9459-174">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d9459-174">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9459-175">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d9459-175">Additional resources</span></span>

* [<span data-ttu-id="d9459-176">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="d9459-176">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
