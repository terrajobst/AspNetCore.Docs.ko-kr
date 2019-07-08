---
title: ASP.NET Core 프로젝트에서 스 캐 폴드 Id
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Identity를 스 캐 폴드 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 9e784864ee78bdfb76dab7748f3fb81728b9c289
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622773"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="afa88-103">ASP.NET Core 프로젝트에서 스 캐 폴드 Id</span><span class="sxs-lookup"><span data-stu-id="afa88-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="afa88-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="afa88-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="afa88-105">ASP.NET Core 2.1 이상에서 제공 [ASP.NET Core Id](xref:security/authentication/identity) 으로 [Razor 클래스 라이브러리](xref:razor-pages/ui-class)합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="afa88-106">Identity를 포함 하는 응용 프로그램 Identity Razor 클래스 라이브러리 (RCL)에 포함 된 소스 코드를 선택적으로 추가할 스 캐 폴더를 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="afa88-107">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="afa88-108">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="afa88-109">생성된 코드는 ID RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="afa88-110">UI의 모든 권한을 얻고 RCL 기본 사용 하지 않는, 참조 섹션 [전체 id UI 원본 만들기](#full)합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="afa88-111">응용 프로그램을 **되지** 포함 인증 RCL Identity 패키지를 추가 하려면 스 캐 폴더를 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="afa88-112">선택한 ID 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="afa88-113">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하기 위해 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="afa88-114">이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="afa88-115">Identity 스 캐 폴더를 실행 하는 경우는 *ScaffoldingReadme.txt* 파일은 프로젝트 디렉터리에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="afa88-116">합니다 *ScaffoldingReadme.txt* 파일 Identity 스 캐 폴딩 업데이트를 완료 하려면 필요한에 대 한 일반 지침을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="afa88-117">이 문서에는 보다 자세한 지침이 포함 되어 있습니다.는 *ScaffoldingReadme.txt* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="afa88-118">파일 차이 보여 줍니다. 변경 내용을에서 백업할 수 있습니다 하는 소스 제어 시스템을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="afa88-119">Identity 스 캐 폴더를 실행 한 후 변경 내용을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="afa88-120">사용 하는 경우 필요한 서비스를 [2 단계 인증이](xref:security/authentication/identity-enable-qrcodes)를 [계정 확인 및 암호 복구](xref:security/authentication/accconfirm), 및 Id와 기타 보안 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="afa88-121">Identity를 스 캐 폴딩 하는 경우 서비스 또는 서비스 스텁을 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="afa88-122">이러한 기능을 사용 하도록 서비스를 수동으로 추가 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="afa88-123">예를 들어, 참조 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="afa88-124">빈 프로젝트에 스 캐 폴드 identity</span><span class="sxs-lookup"><span data-stu-id="afa88-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="afa88-125">다음 강조 표시 된 호출을 추가 합니다 `Startup` 클래스:</span><span class="sxs-lookup"><span data-stu-id="afa88-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="afa88-126">기존 권한 부여 없이 Razor 프로젝트에 스 캐 폴드 identity</span><span class="sxs-lookup"><span data-stu-id="afa88-126">Scaffold identity into a Razor project without existing authorization</span></span>

<!--
set projNam=RPnoAuth
set projType=webapp
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

<span data-ttu-id="afa88-127">id가 구성 되어 *Areas/Identity/IdentityHostingStartup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="afa88-128">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="afa88-129">마이그레이션과 UseAuthentication, 레이아웃</span><span class="sxs-lookup"><span data-stu-id="afa88-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="afa88-130">인증을 사용 하도록 설정</span><span class="sxs-lookup"><span data-stu-id="afa88-130">Enable authentication</span></span>

<span data-ttu-id="afa88-131">에 `Configure` 메서드를 `Startup` 클래스를 호출 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 후 `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="afa88-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="afa88-132">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="afa88-132">Layout changes</span></span>

<span data-ttu-id="afa88-133">선택 사항: 부분 로그인 추가 (`_LoginPartial`) 레이아웃 파일:</span><span class="sxs-lookup"><span data-stu-id="afa88-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="afa88-134">권한 부여를 사용 하 여 Razor 프로젝트에 스 캐 폴드 identity</span><span class="sxs-lookup"><span data-stu-id="afa88-134">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="afa88-135">일부 Id 옵션에 구성 된 *Areas/Identity/IdentityHostingStartup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="afa88-136">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="afa88-137">기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드 identity</span><span class="sxs-lookup"><span data-stu-id="afa88-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="afa88-138">선택 사항: 부분 로그인 추가 (`_LoginPartial`)에 *Views/Shared/_Layout.cshtml* 파일:</span><span class="sxs-lookup"><span data-stu-id="afa88-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="afa88-139">이동 합니다 *Pages/Shared/_LoginPartial.cshtml* 파일을 *Views/Shared/_LoginPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="afa88-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="afa88-140">id가 구성 되어 *Areas/Identity/IdentityHostingStartup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="afa88-141">자세한 내용은 IHostingStartup을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="afa88-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="afa88-142">호출 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 후 `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="afa88-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="afa88-143">권한 부여를 사용 하 여 MVC 프로젝트에 스 캐 폴드 identity</span><span class="sxs-lookup"><span data-stu-id="afa88-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="afa88-144">삭제 된 *페이지/Shared* 폴더 및 해당 폴더의 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="afa88-145">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="afa88-145">Create full identity UI source</span></span>

<span data-ttu-id="afa88-146">Identity UI의 전체 제어를 유지 하려면 Identity 스 캐 폴더를 실행 하 고 선택 **모든 파일 재정의**합니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="afa88-147">다음 강조 표시 된 코드에는 ASP.NET Core 2.1 웹 앱에서 Id를 가진 기본 Identity UI를 바꾸려면 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="afa88-148">Identity UI의 모든 권한을 가질이 작업을 수행 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="afa88-149">다음 코드에서 기본 Identity 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="afa88-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="afa88-150">다음 코드에서는 합니다 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)를 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), 및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="afa88-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="afa88-151">등록을 `IEmailSender` 구현 예를 들어:</span><span class="sxs-lookup"><span data-stu-id="afa88-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="afa88-152">추가 자료</span><span class="sxs-lookup"><span data-stu-id="afa88-152">Additional resources</span></span>

* [<span data-ttu-id="afa88-153">ASP.NET Core 2.1 이상 인증 코드를 변경</span><span class="sxs-lookup"><span data-stu-id="afa88-153">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
