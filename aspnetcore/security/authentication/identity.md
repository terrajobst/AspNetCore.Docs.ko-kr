---
title: ASP.NET Core Identity 소개
author: rick-anderson
description: ASP.NET Core 앱에서 Id를 사용 합니다. 암호 요구 사항 (RequireDigit, RequiredLength, RequiredUniqueChars 등)을 설정 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 12/7/2019
uid: security/authentication/identity
ms.openlocfilehash: 331ebe36eb4bb7fa694de8daa969bcabcab1c974
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803398"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="56cae-104">ASP.NET Core Identity 소개</span><span class="sxs-lookup"><span data-stu-id="56cae-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56cae-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56cae-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="56cae-106">ASP.NET Core Id:</span><span class="sxs-lookup"><span data-stu-id="56cae-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="56cae-107">는 UI (사용자 인터페이스) 로그인 기능을 지 원하는 API입니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="56cae-108">사용자, 암호, 프로필 데이터, 역할, 클레임, 토큰, 전자 메일 확인 등을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="56cae-109">사용자는 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="56cae-110">지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="56cae-111">[Id 소스 코드](https://github.com/aspnet/AspNetCore/tree/master/src/Identity) 는 GitHub에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-111">The [Identity source code](https://github.com/aspnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="56cae-112">Id를 [스 캐 폴드](xref:security/authentication/scaffold-identity) 하 고 생성 된 파일을 확인 하 여 id와의 템플릿 상호 작용을 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="56cae-113">Id는 일반적으로 사용자 이름, 암호 및 프로필 데이터를 저장 하기 위해 SQL Server 데이터베이스를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="56cae-114">또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="56cae-115">이 항목에서는 Id를 사용 하 여 사용자를 등록 하 고 로그인 하 고 로그 아웃 하는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="56cae-116">Id를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="56cae-117">[Microsoft id 플랫폼](/azure/active-directory/develop/) 은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="56cae-118">Azure Active Directory (Azure AD) 개발자 플랫폼의 진화</span><span class="sxs-lookup"><span data-stu-id="56cae-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="56cae-119">ASP.NET Core Id와 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="56cae-120">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="56cae-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="56cae-121">인증을 사용 하 여 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="56cae-121">Create a Web app with authentication</span></span>

<span data-ttu-id="56cae-122">개별 사용자 계정으로 새로운 ASP.NET Core 웹 응용 프로그램 생성하기.</span><span class="sxs-lookup"><span data-stu-id="56cae-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56cae-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56cae-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56cae-124">**File** > **New** > **Project**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="56cae-125">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="56cae-126">프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="56cae-127">**확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-127">Click **OK**.</span></span>
* <span data-ttu-id="56cae-128">ASP.NET Core **웹 응용 프로그램**을 선택한 다음 **인증 변경**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="56cae-129">**개별 사용자 계정을** 선택 하 고 **확인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56cae-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="56cae-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="56cae-131">이전 명령은 SQLite를 사용 하 여 Razor 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="56cae-132">LocalDB를 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="56cae-133">생성 된 프로젝트는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="56cae-134">Id Razor 클래스 라이브러리는 `Identity` 영역이 있는 끝점을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="56cae-135">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="56cae-135">For example:</span></span>

* <span data-ttu-id="56cae-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="56cae-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="56cae-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="56cae-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="56cae-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="56cae-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="56cae-139">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="56cae-139">Apply migrations</span></span>

<span data-ttu-id="56cae-140">마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56cae-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56cae-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56cae-142">패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56cae-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="56cae-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="56cae-144">SQLite를 사용 하는 경우이 단계에서는 마이그레이션이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="56cae-145">LocalDB의 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="56cae-146">테스트 등록 및 로그인</span><span class="sxs-lookup"><span data-stu-id="56cae-146">Test Register and Login</span></span>

<span data-ttu-id="56cae-147">앱을 실행 하 고 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-147">Run the app and register a user.</span></span> <span data-ttu-id="56cae-148">화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="56cae-149">Id 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="56cae-149">Configure Identity services</span></span>

<span data-ttu-id="56cae-150">서비스는 `ConfigureServices`에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="56cae-151">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="56cae-152">위의 강조 표시 된 코드는 기본 옵션 값을 사용 하 여 Id를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="56cae-153">서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="56cae-154"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>를 호출 하 여 id를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="56cae-155">`UseAuthentication`은 요청 파이프라인에 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="56cae-156">템플릿에서 생성 된 앱은 [권한 부여](xref:security/authorization/secure-data)를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="56cae-157">앱이 권한 부여를 추가 하는 것이 올바른 순서로 추가 되도록 `app.UseAuthorization` 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="56cae-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`및 `UseEndpoints`는 앞의 코드에 표시 된 순서 대로 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="56cae-159">`IdentityOptions` 및 `Startup`에 대 한 자세한 내용은 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="56cae-160">스 캐 폴드 Register, Login 및 LogOut</span><span class="sxs-lookup"><span data-stu-id="56cae-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56cae-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56cae-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56cae-162">Register, Login 및 LogOut 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="56cae-163">이 섹션에 표시 된 코드를 생성 하려면 [권한 부여 지침을 사용 하 여 스 캐 폴드 id를 Razor 프로젝트로](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56cae-164">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="56cae-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="56cae-165">**WebApp1**이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="56cae-166">그렇지 않으면 `ApplicationDbContext`에 대해 올바른 네임 스페이스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="56cae-167">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="56cae-168">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="56cae-169">스 캐 폴딩 Id에 대 한 자세한 내용은 [스 캐 폴드 identity to a Razor project to authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="56cae-170">등록 검사</span><span class="sxs-lookup"><span data-stu-id="56cae-170">Examine Register</span></span>

<span data-ttu-id="56cae-171">사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="56cae-172">사용자는 `_userManager` 개체의 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="56cae-173">`_userManager`는 종속성 주입에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="56cae-174">사용자가 정상적으로 생성되면 `_signInManager.SignInAsync` 가 호출되어 사용자가 즉시 로그인됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="56cae-175">등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="56cae-176">로그인</span><span class="sxs-lookup"><span data-stu-id="56cae-176">Log in</span></span>

<span data-ttu-id="56cae-177">다음과 같은 경우 로그인 양식이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="56cae-178">**로그인** 링크가 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="56cae-179">사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="56cae-180">로그인 페이지의 양식이 제출 되 면 `OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="56cae-181">`PasswordSignInAsync`는 종속성 주입에 의해 제공 되는 `_signInManager` 개체에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="56cae-182">기본 `Controller` 클래스는 컨트롤러 메서드에서 액세스할 수 있는 `User` 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="56cae-183">예를 들어 `User.Claims`를 열거 하 고 권한 부여 결정을 내릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="56cae-184">자세한 내용은 <xref:security/authorization/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="56cae-185">로그아웃</span><span class="sxs-lookup"><span data-stu-id="56cae-185">Log out</span></span>

<span data-ttu-id="56cae-186">**로그 아웃** 링크는 `LogoutModel.OnPost` 동작을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="56cae-187">위의 코드에서는 브라우저가 새 요청을 수행 하 고 사용자에 대 한 id가 업데이트 되도록 코드 `return RedirectToPage();` 리디렉션이 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="56cae-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 된 사용자의 클레임을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="56cae-189">Post는 *Pages/Shared/_LoginPartial*에서 지정 됩니다. cshtml:</span><span class="sxs-lookup"><span data-stu-id="56cae-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="56cae-190">테스트 Id</span><span class="sxs-lookup"><span data-stu-id="56cae-190">Test Identity</span></span>

<span data-ttu-id="56cae-191">기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="56cae-192">Id를 테스트 하려면 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="56cae-193">로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="56cae-194">로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="56cae-195">Id 탐색</span><span class="sxs-lookup"><span data-stu-id="56cae-195">Explore Identity</span></span>

<span data-ttu-id="56cae-196">Id를 자세히 살펴보려면:</span><span class="sxs-lookup"><span data-stu-id="56cae-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="56cae-197">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="56cae-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="56cae-198">각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="56cae-199">Id 구성 요소</span><span class="sxs-lookup"><span data-stu-id="56cae-199">Identity Components</span></span>

<span data-ttu-id="56cae-200">모든 Id 종속 NuGet 패키지는 [ASP.NET Core 공유 프레임 워크](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="56cae-201">Id에 대 한 기본 패키지는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)입니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="56cae-202">ASP.NET Core Identity에 대한 주요 인터페이스 모음을 포함하고 있는 이 패키지는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="56cae-203">ASP.NET Identity로 마이그레이션하기</span><span class="sxs-lookup"><span data-stu-id="56cae-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="56cae-204">기존 Id 저장소를 마이그레이션하는 방법에 대 한 자세한 내용 및 지침은 [인증 및 Id 마이그레이션](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="56cae-205">암호 강도 설정</span><span class="sxs-lookup"><span data-stu-id="56cae-205">Setting password strength</span></span>

<span data-ttu-id="56cae-206">최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="56cae-207">AddDefaultIdentity 및 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="56cae-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="56cae-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="56cae-209">`AddDefaultIdentity`를 호출 하는 것은 다음을 호출 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="56cae-210">자세한 내용은 [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-210">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="56cae-211">다음 단계</span><span class="sxs-lookup"><span data-stu-id="56cae-211">Next Steps</span></span>

* [<span data-ttu-id="56cae-212">ID 구성</span><span class="sxs-lookup"><span data-stu-id="56cae-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56cae-213">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56cae-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="56cae-214">ASP.NET Core Id는 ASP.NET Core 앱에 로그인 기능을 추가 하는 멤버 자격 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="56cae-215">사용자는 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="56cae-216">지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="56cae-217">SQL Server 데이터베이스를 사용 하 여 id를 구성 하 여 사용자 이름, 암호 및 프로필 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="56cae-218">또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="56cae-219">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="56cae-219">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="56cae-220">이 항목에서는 Id를 사용 하 여 사용자를 등록 하 고 로그인 하 고 로그 아웃 하는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="56cae-221">Id를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="56cae-222">AddDefaultIdentity 및 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="56cae-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="56cae-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="56cae-224">`AddDefaultIdentity`를 호출 하는 것은 다음을 호출 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="56cae-225">자세한 내용은 [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-225">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="56cae-226">인증을 사용 하 여 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="56cae-226">Create a Web app with authentication</span></span>

<span data-ttu-id="56cae-227">개별 사용자 계정으로 새로운 ASP.NET Core 웹 응용 프로그램 생성하기.</span><span class="sxs-lookup"><span data-stu-id="56cae-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56cae-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56cae-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56cae-229">**File** > **New** > **Project**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="56cae-230">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="56cae-231">프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="56cae-232">**확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-232">Click **OK**.</span></span>
* <span data-ttu-id="56cae-233">ASP.NET Core **웹 응용 프로그램**을 선택한 다음 **인증 변경**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="56cae-234">**개별 사용자 계정을** 선택 하 고 **확인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56cae-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="56cae-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="56cae-236">생성 된 프로젝트는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="56cae-237">Id Razor 클래스 라이브러리는 `Identity` 영역이 있는 끝점을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="56cae-238">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="56cae-238">For example:</span></span>

* <span data-ttu-id="56cae-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="56cae-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="56cae-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="56cae-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="56cae-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="56cae-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="56cae-242">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="56cae-242">Apply migrations</span></span>

<span data-ttu-id="56cae-243">마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56cae-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56cae-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56cae-245">패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-245">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56cae-246">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="56cae-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="56cae-247">테스트 등록 및 로그인</span><span class="sxs-lookup"><span data-stu-id="56cae-247">Test Register and Login</span></span>

<span data-ttu-id="56cae-248">앱을 실행 하 고 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-248">Run the app and register a user.</span></span> <span data-ttu-id="56cae-249">화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="56cae-250">Id 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="56cae-250">Configure Identity services</span></span>

<span data-ttu-id="56cae-251">서비스는 `ConfigureServices`에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="56cae-252">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="56cae-253">위의 코드는 기본 옵션 값을 사용 하 여 Id를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="56cae-254">서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="56cae-255">[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 id를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="56cae-256">`UseAuthentication`은 요청 파이프라인에 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="56cae-257">자세한 내용은 [IdentityOptions 클래스](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="56cae-258">스 캐 폴드 Register, Login 및 LogOut</span><span class="sxs-lookup"><span data-stu-id="56cae-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="56cae-259">이 섹션에 표시 된 코드를 생성 하려면 [권한 부여 지침을 사용 하 여 스 캐 폴드 id를 Razor 프로젝트로](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56cae-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56cae-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56cae-261">Register, Login 및 LogOut 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56cae-262">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="56cae-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="56cae-263">**WebApp1**이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="56cae-264">그렇지 않으면 `ApplicationDbContext`에 대해 올바른 네임 스페이스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="56cae-265">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="56cae-266">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="56cae-267">등록 검사</span><span class="sxs-lookup"><span data-stu-id="56cae-267">Examine Register</span></span>

<span data-ttu-id="56cae-268">사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="56cae-269">사용자는 `_userManager` 개체의 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="56cae-270">`_userManager`는 종속성 주입에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-270">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="56cae-271">사용자가 정상적으로 생성되면 `_signInManager.SignInAsync` 가 호출되어 사용자가 즉시 로그인됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="56cae-272">**참고:** 사용자 등록 즉시 로그인을 방지하는 방법은 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="56cae-273">로그인</span><span class="sxs-lookup"><span data-stu-id="56cae-273">Log in</span></span>

<span data-ttu-id="56cae-274">다음과 같은 경우 로그인 양식이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="56cae-275">**로그인** 링크가 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="56cae-276">사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="56cae-277">로그인 페이지의 양식이 제출 되 면 `OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="56cae-278">`PasswordSignInAsync`는 종속성 주입에 의해 제공 되는 `_signInManager` 개체에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-278">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="56cae-279">기본 `Controller` 클래스는 컨트롤러의 메서드에서 접근할 수 있는 `User` 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-279">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="56cae-280">예를 들어 `User.Claims`를 열거 하 고 권한 부여 결정을 내릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-280">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="56cae-281">자세한 내용은 <xref:security/authorization/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-281">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="56cae-282">로그아웃</span><span class="sxs-lookup"><span data-stu-id="56cae-282">Log out</span></span>

<span data-ttu-id="56cae-283">**로그 아웃** 링크는 `LogoutModel.OnPost` 동작을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="56cae-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 된 사용자의 클레임을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="56cae-285">Post는 *Pages/Shared/_LoginPartial*에서 지정 됩니다. cshtml:</span><span class="sxs-lookup"><span data-stu-id="56cae-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="56cae-286">테스트 Id</span><span class="sxs-lookup"><span data-stu-id="56cae-286">Test Identity</span></span>

<span data-ttu-id="56cae-287">기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="56cae-288">Id를 테스트 하려면 개인 정보 페이지에 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="56cae-289">로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="56cae-290">로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="56cae-291">Id 탐색</span><span class="sxs-lookup"><span data-stu-id="56cae-291">Explore Identity</span></span>

<span data-ttu-id="56cae-292">Id를 자세히 살펴보려면:</span><span class="sxs-lookup"><span data-stu-id="56cae-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="56cae-293">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="56cae-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="56cae-294">각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="56cae-295">Id 구성 요소</span><span class="sxs-lookup"><span data-stu-id="56cae-295">Identity Components</span></span>

<span data-ttu-id="56cae-296">모든 Id 종속 NuGet 패키지는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="56cae-297">Id에 대 한 기본 패키지는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)입니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="56cae-298">ASP.NET Core Identity에 대한 주요 인터페이스 모음을 포함하고 있는 이 패키지는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56cae-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="56cae-299">ASP.NET Identity로 마이그레이션하기</span><span class="sxs-lookup"><span data-stu-id="56cae-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="56cae-300">기존 Id 저장소를 마이그레이션하는 방법에 대 한 자세한 내용 및 지침은 [인증 및 Id 마이그레이션](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="56cae-301">암호 강도 설정</span><span class="sxs-lookup"><span data-stu-id="56cae-301">Setting password strength</span></span>

<span data-ttu-id="56cae-302">최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="56cae-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="56cae-303">다음 단계</span><span class="sxs-lookup"><span data-stu-id="56cae-303">Next Steps</span></span>

* [<span data-ttu-id="56cae-304">ID 구성</span><span class="sxs-lookup"><span data-stu-id="56cae-304">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
