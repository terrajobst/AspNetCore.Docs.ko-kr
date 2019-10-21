---
title: ASP.NET Core의 Id 소개
author: rick-anderson
description: ASP.NET Core 앱에서 Id를 사용 합니다. 암호 요구 사항 (RequireDigit, RequiredLength, RequiredUniqueChars 등)을 설정 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 10/15/2019
uid: security/authentication/identity
ms.openlocfilehash: 8da13ca5f74a9c829eb8137d33af0684ff88266d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333568"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="2fde5-104">ASP.NET Core의 Id 소개</span><span class="sxs-lookup"><span data-stu-id="2fde5-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fde5-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2fde5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2fde5-106">ASP.NET Core Id는 UI (사용자 인터페이스) 로그인 기능을 지 원하는 멤버 자격 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-106">ASP.NET Core Identity is a membership system that supports user interface (UI) login functionality.</span></span> <span data-ttu-id="2fde5-107">사용자는 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="2fde5-108">지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="2fde5-109">SQL Server 데이터베이스를 사용 하 여 id를 구성 하 여 사용자 이름, 암호 및 프로필 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="2fde5-110">또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="2fde5-111">이 항목에서는 Id를 사용 하 여 사용자를 등록 하 고 로그인 하 고 로그 아웃 하는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-111">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="2fde5-112">Id를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-112">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="2fde5-113">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2fde5-113">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="2fde5-114">인증을 사용 하 여 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="2fde5-114">Create a Web app with authentication</span></span>

<span data-ttu-id="2fde5-115">개별 사용자 계정을 사용 하 여 ASP.NET Core 웹 응용 프로그램 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-115">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fde5-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fde5-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2fde5-117">**파일** > **새** > **프로젝트**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-117">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="2fde5-118">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2fde5-119">프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-119">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="2fde5-120">**확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-120">Click **OK**.</span></span>
* <span data-ttu-id="2fde5-121">ASP.NET Core **웹 응용 프로그램**을 선택한 다음 **인증 변경**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-121">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="2fde5-122">**개별 사용자 계정을** 선택 하 고 **확인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-122">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fde5-123">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2fde5-123">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="2fde5-124">이전 명령은 SQLite를 사용 하 여 Razor 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-124">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="2fde5-125">LocalDB를 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-125">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="2fde5-126">생성 된 프로젝트는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-126">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2fde5-127">Id Razor 클래스 라이브러리는 `Identity` 영역이 있는 끝점을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-127">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="2fde5-128">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="2fde5-128">For example:</span></span>

* <span data-ttu-id="2fde5-129">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="2fde5-129">/Identity/Account/Login</span></span>
* <span data-ttu-id="2fde5-130">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="2fde5-130">/Identity/Account/Logout</span></span>
* <span data-ttu-id="2fde5-131">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="2fde5-131">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="2fde5-132">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="2fde5-132">Apply migrations</span></span>

<span data-ttu-id="2fde5-133">마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-133">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fde5-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fde5-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fde5-135">패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-135">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fde5-136">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2fde5-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2fde5-137">SQLite를 사용 하는 경우이 단계에서는 마이그레이션이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-137">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="2fde5-138">LocalDB의 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-138">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="2fde5-139">테스트 등록 및 로그인</span><span class="sxs-lookup"><span data-stu-id="2fde5-139">Test Register and Login</span></span>

<span data-ttu-id="2fde5-140">앱을 실행 하 고 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-140">Run the app and register a user.</span></span> <span data-ttu-id="2fde5-141">화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-141">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="2fde5-142">Id 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="2fde5-142">Configure Identity services</span></span>

<span data-ttu-id="2fde5-143">서비스는 `ConfigureServices`에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-143">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="2fde5-144">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-144">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="2fde5-145">위의 강조 표시 된 코드는 기본 옵션 값을 사용 하 여 Id를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-145">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="2fde5-146">서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-146">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2fde5-147">@No__t_0를 호출 하 여 id를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-147">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="2fde5-148">`UseAuthentication`는 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-148">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="2fde5-149">템플릿에서 생성 된 앱은 [권한 부여](xref:security/authorization/secure-data)를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-149">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="2fde5-150">앱이 권한 부여를 추가 하는 것이 올바른 순서로 추가 되도록 `app.UseAuthorization` 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-150">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="2fde5-151">`UseRouting`, `UseAuthentication`, `UseAuthorization` 및 `UseEndpoints`는 앞의 코드에 표시 된 순서 대로 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-151">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="2fde5-152">@No__t_0 및 `Startup`에 대 한 자세한 내용은 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-152">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="2fde5-153">스 캐 폴드 Register, Login 및 LogOut</span><span class="sxs-lookup"><span data-stu-id="2fde5-153">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fde5-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fde5-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fde5-155">Register, Login 및 LogOut 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-155">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="2fde5-156">이 섹션에 표시 된 코드를 생성 하려면 [권한 부여 지침을 사용 하 여 스 캐 폴드 id를 Razor 프로젝트로](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-156">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fde5-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2fde5-157">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2fde5-158">**WebApp1**이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-158">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="2fde5-159">그렇지 않으면 `ApplicationDbContext`에 대해 올바른 네임 스페이스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-159">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="2fde5-160">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-160">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="2fde5-161">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-161">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="2fde5-162">스 캐 폴딩 Id에 대 한 자세한 내용은 [스 캐 폴드 identity to a Razor project to authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-162">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="2fde5-163">등록 검사</span><span class="sxs-lookup"><span data-stu-id="2fde5-163">Examine Register</span></span>

<span data-ttu-id="2fde5-164">사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-164">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="2fde5-165">사용자는 `_userManager` 개체의 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-165">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="2fde5-166">`_userManager`는 종속성 주입에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-166">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="2fde5-167">사용자가 성공적으로 만들어진 경우 `_signInManager.SignInAsync`에 대 한 호출을 통해 사용자가 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-167">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="2fde5-168">등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-168">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="2fde5-169">로그인</span><span class="sxs-lookup"><span data-stu-id="2fde5-169">Log in</span></span>

<span data-ttu-id="2fde5-170">다음과 같은 경우 로그인 양식이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-170">The Login form is displayed when:</span></span>

* <span data-ttu-id="2fde5-171">**로그인** 링크가 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-171">The **Log in** link is selected.</span></span>
* <span data-ttu-id="2fde5-172">사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-172">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="2fde5-173">로그인 페이지의 양식이 제출 되 면 `OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-173">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="2fde5-174">`PasswordSignInAsync`는 종속성 주입에 의해 제공 되는 `_signInManager` 개체에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-174">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="2fde5-175">기본 `Controller` 클래스는 컨트롤러 메서드에서 액세스할 수 있는 `User` 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-175">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="2fde5-176">예를 들어 `User.Claims`를 열거 하 고 권한 부여 결정을 내릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-176">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="2fde5-177">자세한 내용은 <xref:security/authorization/introduction>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="2fde5-177">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="2fde5-178">로그 아웃</span><span class="sxs-lookup"><span data-stu-id="2fde5-178">Log out</span></span>

<span data-ttu-id="2fde5-179">**로그 아웃** 링크는 `LogoutModel.OnPost` 동작을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-179">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="2fde5-180">위의 코드에서는 브라우저가 새 요청을 수행 하 고 사용자에 대 한 id가 업데이트 되도록 코드 `return RedirectToPage();` 리디렉션이 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-180">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="2fde5-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 된 사용자의 클레임을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="2fde5-182">Post는 *Pages/Shared/_LoginPartial*에서 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-182">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="2fde5-183">테스트 Id</span><span class="sxs-lookup"><span data-stu-id="2fde5-183">Test Identity</span></span>

<span data-ttu-id="2fde5-184">기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-184">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="2fde5-185">Id를 테스트 하려면 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-185">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="2fde5-186">로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-186">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="2fde5-187">로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-187">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="2fde5-188">Id 탐색</span><span class="sxs-lookup"><span data-stu-id="2fde5-188">Explore Identity</span></span>

<span data-ttu-id="2fde5-189">Id를 자세히 살펴보려면:</span><span class="sxs-lookup"><span data-stu-id="2fde5-189">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="2fde5-190">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="2fde5-190">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="2fde5-191">각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-191">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="2fde5-192">Id 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2fde5-192">Identity Components</span></span>

<span data-ttu-id="2fde5-193">모든 Id 종속 NuGet 패키지는 [ASP.NET Core 공유 프레임 워크](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-193">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="2fde5-194">Id에 대 한 기본 패키지는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)입니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-194">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="2fde5-195">이 패키지에는 ASP.NET Core Id의 핵심 인터페이스 집합이 포함 되어 있으며 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-195">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="2fde5-196">ASP.NET Core Id로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="2fde5-196">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="2fde5-197">기존 Id 저장소를 마이그레이션하는 방법에 대 한 자세한 내용 및 지침은 [인증 및 Id 마이그레이션](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-197">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="2fde5-198">암호 강도 설정</span><span class="sxs-lookup"><span data-stu-id="2fde5-198">Setting password strength</span></span>

<span data-ttu-id="2fde5-199">최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-199">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="2fde5-200">AddDefaultIdentity 및 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="2fde5-200">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="2fde5-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="2fde5-202">@No__t_0를 호출 하는 것은 다음을 호출 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-202">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="2fde5-203">자세한 내용은 [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-203">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2fde5-204">다음 단계</span><span class="sxs-lookup"><span data-stu-id="2fde5-204">Next Steps</span></span>

* [<span data-ttu-id="2fde5-205">ID 구성</span><span class="sxs-lookup"><span data-stu-id="2fde5-205">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fde5-206">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2fde5-206">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2fde5-207">ASP.NET Core Id는 ASP.NET Core 앱에 로그인 기능을 추가 하는 멤버 자격 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-207">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="2fde5-208">사용자는 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-208">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="2fde5-209">지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-209">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="2fde5-210">SQL Server 데이터베이스를 사용 하 여 id를 구성 하 여 사용자 이름, 암호 및 프로필 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-210">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="2fde5-211">또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-211">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="2fde5-212">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2fde5-212">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2fde5-213">이 항목에서는 Id를 사용 하 여 사용자를 등록 하 고 로그인 하 고 로그 아웃 하는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-213">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="2fde5-214">Id를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-214">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="2fde5-215">AddDefaultIdentity 및 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="2fde5-215">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="2fde5-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="2fde5-217">@No__t_0를 호출 하는 것은 다음을 호출 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-217">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="2fde5-218">자세한 내용은 [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-218">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="2fde5-219">인증을 사용 하 여 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="2fde5-219">Create a Web app with authentication</span></span>

<span data-ttu-id="2fde5-220">개별 사용자 계정을 사용 하 여 ASP.NET Core 웹 응용 프로그램 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-220">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fde5-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fde5-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2fde5-222">**파일** > **새** > **프로젝트**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-222">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="2fde5-223">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-223">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2fde5-224">프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-224">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="2fde5-225">**확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-225">Click **OK**.</span></span>
* <span data-ttu-id="2fde5-226">ASP.NET Core **웹 응용 프로그램**을 선택한 다음 **인증 변경**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-226">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="2fde5-227">**개별 사용자 계정을** 선택 하 고 **확인**을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-227">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fde5-228">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2fde5-228">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="2fde5-229">생성 된 프로젝트는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-229">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2fde5-230">Id Razor 클래스 라이브러리는 `Identity` 영역이 있는 끝점을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-230">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="2fde5-231">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="2fde5-231">For example:</span></span>

* <span data-ttu-id="2fde5-232">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="2fde5-232">/Identity/Account/Login</span></span>
* <span data-ttu-id="2fde5-233">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="2fde5-233">/Identity/Account/Logout</span></span>
* <span data-ttu-id="2fde5-234">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="2fde5-234">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="2fde5-235">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="2fde5-235">Apply migrations</span></span>

<span data-ttu-id="2fde5-236">마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-236">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fde5-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fde5-237">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fde5-238">패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-238">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fde5-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2fde5-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="2fde5-240">테스트 등록 및 로그인</span><span class="sxs-lookup"><span data-stu-id="2fde5-240">Test Register and Login</span></span>

<span data-ttu-id="2fde5-241">앱을 실행 하 고 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-241">Run the app and register a user.</span></span> <span data-ttu-id="2fde5-242">화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-242">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="2fde5-243">Id 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="2fde5-243">Configure Identity services</span></span>

<span data-ttu-id="2fde5-244">서비스는 `ConfigureServices`에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-244">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="2fde5-245">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-245">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="2fde5-246">위의 코드는 기본 옵션 값을 사용 하 여 Id를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-246">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="2fde5-247">서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-247">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2fde5-248">[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 id를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-248">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="2fde5-249">`UseAuthentication`는 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-249">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="2fde5-250">자세한 내용은 [IdentityOptions 클래스](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-250">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="2fde5-251">스 캐 폴드 Register, Login 및 LogOut</span><span class="sxs-lookup"><span data-stu-id="2fde5-251">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="2fde5-252">이 섹션에 표시 된 코드를 생성 하려면 [권한 부여 지침을 사용 하 여 스 캐 폴드 id를 Razor 프로젝트로](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-252">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fde5-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fde5-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fde5-254">Register, Login 및 LogOut 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-254">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2fde5-255">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2fde5-255">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2fde5-256">**WebApp1**이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-256">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="2fde5-257">그렇지 않으면 `ApplicationDbContext`에 대해 올바른 네임 스페이스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-257">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="2fde5-258">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-258">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="2fde5-259">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-259">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="2fde5-260">등록 검사</span><span class="sxs-lookup"><span data-stu-id="2fde5-260">Examine Register</span></span>

<span data-ttu-id="2fde5-261">사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-261">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="2fde5-262">사용자는 `_userManager` 개체의 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-262">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="2fde5-263">`_userManager`는 종속성 주입에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-263">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="2fde5-264">사용자가 성공적으로 만들어진 경우 `_signInManager.SignInAsync`에 대 한 호출을 통해 사용자가 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-264">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="2fde5-265">**참고:** 등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-265">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="2fde5-266">로그인</span><span class="sxs-lookup"><span data-stu-id="2fde5-266">Log in</span></span>

<span data-ttu-id="2fde5-267">다음과 같은 경우 로그인 양식이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-267">The Login form is displayed when:</span></span>

* <span data-ttu-id="2fde5-268">**로그인** 링크가 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-268">The **Log in** link is selected.</span></span>
* <span data-ttu-id="2fde5-269">사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-269">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="2fde5-270">로그인 페이지의 양식이 제출 되 면 `OnPostAsync` 작업이 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-270">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="2fde5-271">`PasswordSignInAsync`는 종속성 주입에 의해 제공 되는 `_signInManager` 개체에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-271">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="2fde5-272">기본 `Controller` 클래스는 컨트롤러 메서드에서 액세스할 수 있는 `User` 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-272">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="2fde5-273">예를 들어 `User.Claims`를 열거 하 고 권한 부여 결정을 내릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-273">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="2fde5-274">자세한 내용은 <xref:security/authorization/introduction>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="2fde5-274">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="2fde5-275">로그 아웃</span><span class="sxs-lookup"><span data-stu-id="2fde5-275">Log out</span></span>

<span data-ttu-id="2fde5-276">**로그 아웃** 링크는 `LogoutModel.OnPost` 동작을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-276">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="2fde5-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 된 사용자의 클레임을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="2fde5-278">Post는 *Pages/Shared/_LoginPartial*에서 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-278">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="2fde5-279">테스트 Id</span><span class="sxs-lookup"><span data-stu-id="2fde5-279">Test Identity</span></span>

<span data-ttu-id="2fde5-280">기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-280">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="2fde5-281">Id를 테스트 하려면 개인 정보 페이지에 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-281">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="2fde5-282">로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-282">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="2fde5-283">로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-283">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="2fde5-284">Id 탐색</span><span class="sxs-lookup"><span data-stu-id="2fde5-284">Explore Identity</span></span>

<span data-ttu-id="2fde5-285">Id를 자세히 살펴보려면:</span><span class="sxs-lookup"><span data-stu-id="2fde5-285">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="2fde5-286">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="2fde5-286">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="2fde5-287">각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-287">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="2fde5-288">Id 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2fde5-288">Identity Components</span></span>

<span data-ttu-id="2fde5-289">모든 Id 종속 NuGet 패키지는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-289">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2fde5-290">Id에 대 한 기본 패키지는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)입니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-290">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="2fde5-291">이 패키지에는 ASP.NET Core Id의 핵심 인터페이스 집합이 포함 되어 있으며 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2fde5-291">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="2fde5-292">ASP.NET Core Id로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="2fde5-292">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="2fde5-293">기존 Id 저장소를 마이그레이션하는 방법에 대 한 자세한 내용 및 지침은 [인증 및 Id 마이그레이션](xref:migration/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-293">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="2fde5-294">암호 강도 설정</span><span class="sxs-lookup"><span data-stu-id="2fde5-294">Setting password strength</span></span>

<span data-ttu-id="2fde5-295">최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2fde5-295">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2fde5-296">다음 단계</span><span class="sxs-lookup"><span data-stu-id="2fde5-296">Next Steps</span></span>

* [<span data-ttu-id="2fde5-297">ID 구성</span><span class="sxs-lookup"><span data-stu-id="2fde5-297">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
