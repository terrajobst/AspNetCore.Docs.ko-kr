---
title: ASP.NET Core Identity 소개
author: rick-anderson
description: ASP.NET Core 앱을 사용 하 여 Id를 사용 합니다. 암호 요구 사항 (RequireDigit, RequiredLength, RequiredUniqueChars, 등)을 설정 하는 방법에 알아봅니다.
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: d813fa364bb733185baa7b2cd2d95f8b4ff570e2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894330"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="c506c-104">ASP.NET Core Identity 소개</span><span class="sxs-lookup"><span data-stu-id="c506c-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="c506c-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c506c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c506c-106">ASP.NET Core Id는 ASP.NET Core 앱에 로그인 기능을 추가 하는 멤버 자격 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="c506c-107">사용자 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들 수 있습니다 하거나 외부 로그인 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="c506c-108">지원 되는 외부 로그인 공급자를 포함 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="c506c-109">사용자 이름, 암호 및 프로필 데이터를 저장 하는 SQL Server 데이터베이스를 사용 하 여 id는 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="c506c-110">또는 다른 영구 저장소 예를 들어, Azure Table Storage 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="c506c-111">[보기 또는 다운로드 샘플 코드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([다운로드 하는 방법)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c506c-111">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c506c-112">이 항목의 Id를 사용 하 여 등록, 로그인 하는 방법을 알아보고는 사용자를 로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="c506c-113">Identity를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c506c-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="c506c-114">AddDefaultIdentity 및 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="c506c-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="c506c-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) ASP.NET Core 2.1에서 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="c506c-116">호출 `AddDefaultIdentity` 다음 호출 하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="c506c-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="c506c-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="c506c-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="c506c-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="c506c-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="c506c-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="c506c-120">참조 [AddDefaultIdentity 원본](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="c506c-121">인증을 사용 하 여 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="c506c-121">Create a Web app with authentication</span></span>

<span data-ttu-id="c506c-122">개별 사용자 계정으로 새로운 ASP.NET Core 웹 응용 프로그램 생성하기.</span><span class="sxs-lookup"><span data-stu-id="c506c-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c506c-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c506c-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c506c-124">**파일** > **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="c506c-125">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c506c-126">프로젝트 이름을 **WebApp1** 프로젝트 다운로드와 같은 네임 스페이스에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="c506c-127">**확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-127">Click **OK**.</span></span>
* <span data-ttu-id="c506c-128">ASP.NET Core를 선택 **웹 응용 프로그램**을 선택한 후 **인증 변경**합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="c506c-129">선택 **개별 사용자 계정** 누릅니다 **확인**합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c506c-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c506c-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="c506c-131">생성된 된 프로젝트를 제공 [ASP.NET Core Id](xref:security/authentication/identity) 으로 [Razor 클래스 라이브러리](xref:razor-pages/ui-class)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c506c-132">사용 하 여 끝점을 노출 하는 Identity Razor 클래스 라이브러리는 `Identity` 영역입니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="c506c-133">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="c506c-133">For example:</span></span>

* <span data-ttu-id="c506c-134">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="c506c-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="c506c-135">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="c506c-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="c506c-136">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="c506c-136">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="c506c-137">마이그레이션 적용</span><span class="sxs-lookup"><span data-stu-id="c506c-137">Apply migrations</span></span>

<span data-ttu-id="c506c-138">데이터베이스를 초기화 하 여 마이그레이션을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-138">Apply the migrations to initialise the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c506c-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c506c-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c506c-140">다음 명령은 패키지 관리자 콘솔 (PMC)에서 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-140">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c506c-141">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c506c-141">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="c506c-142">테스트 등록 및 로그인</span><span class="sxs-lookup"><span data-stu-id="c506c-142">Test Register and Login</span></span>

<span data-ttu-id="c506c-143">앱을 실행 하 고 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-143">Run the app and register a user.</span></span> <span data-ttu-id="c506c-144">화면 크기에 따라 탐색 설정/해제 단추를 선택 해야 합니다 **등록** 하 고 **로그인** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-144">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="c506c-145">Id 서비스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-145">Configure Identity services</span></span>

<span data-ttu-id="c506c-146">서비스에 추가 된 `ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-146">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="c506c-147">일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-147">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="c506c-148">위의 코드는 기본 옵션 값을 사용 하 여 Identity를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-148">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="c506c-149">서비스를 통해 앱에 제공 됩니다 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-149">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="c506c-150">호출 하 여 id를 활성화 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-150">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="c506c-151">`UseAuthentication`은 요청 파이프라인에 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-151">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="c506c-152">서비스를 통해 응용 프로그램에 제공 됩니다 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-152">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="c506c-153">`Configure` 메서드에서 `UseAuthentication` 메서드를 호출하면 응용 프로그램에서 Identity가 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-153">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="c506c-154">`UseAuthentication`은 요청 파이프라인에 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-154">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="c506c-155">이 서비스들은 응용 프로그램에서 [종속성 주입](xref:fundamentals/dependency-injection)을 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-155">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="c506c-156">`Configure` 메서드에서 `UseIdentity` 메서드를 호출하면 응용 프로그램에서 Identity가 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-156">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="c506c-157">`UseIdentity`는 요청 파이프라인에 쿠키 기반의 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-157">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="c506c-158">자세한 내용은 참조는 [IdentityOptions 클래스](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 하 고 [응용 프로그램 시작](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c506c-158">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="c506c-159">스 캐 폴드 등록, 로그인 및 로그 아웃</span><span class="sxs-lookup"><span data-stu-id="c506c-159">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="c506c-160">수행 합니다 [identity 권한 부여를 사용 하 여 Razor 프로젝트에 스 캐 폴드](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이 섹션에 나와 있는 코드를 생성 하는 지침입니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-160">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c506c-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c506c-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c506c-162">등록, 로그인 및 로그 아웃 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-162">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c506c-163">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c506c-163">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c506c-164">이름으로 프로젝트를 만든 경우 **WebApp1**, 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-164">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="c506c-165">그렇지 않은 경우에 대 한 올바른 네임 스페이스를 사용 합니다 `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="c506c-165">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c506c-166">PowerShell 명령 구분 기호로 세미콜론을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-166">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c506c-167">PowerShell을 사용 하는 경우 파일 목록에 세미콜론을 이스케이프 하거나 이전 예제와 같이 큰따옴표로 파일 목록을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-167">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="c506c-168">등록 확인</span><span class="sxs-lookup"><span data-stu-id="c506c-168">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="c506c-169">클릭할 때 합니다 **등록** 링크를 `RegisterModel.OnPostAsync` 동작이 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-169">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="c506c-170">사용자가 만든 [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 에 `_userManager` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-170">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="c506c-171">`_userManager` 가 제공한 종속성 주입):</span><span class="sxs-lookup"><span data-stu-id="c506c-171">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="c506c-172">클릭할 때 합니다 **등록** 링크를 `Register` 에서 동작이 호출 될 `AccountController`.</span><span class="sxs-lookup"><span data-stu-id="c506c-172">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="c506c-173">`Register` 액션은 `_userManager` 개체의 (종속성 주입으로 `AccountController`에 제공된) `CreateAsync`를 호출해서 사용자를 생성합니다:</span><span class="sxs-lookup"><span data-stu-id="c506c-173">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="c506c-174">사용자가 정상적으로 생성되면 `_signInManager.SignInAsync` 가 호출되어 사용자가 즉시 로그인됩니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="c506c-175">**참고:** 참조 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 등록 시 즉시 로그인을 방지 하기 위한 단계에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-175">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="c506c-176">로그인</span><span class="sxs-lookup"><span data-stu-id="c506c-176">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c506c-177">로그인 양식이 표시 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="c506c-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="c506c-178">합니다 **로그인** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="c506c-179">사용자가 이러한 권한이 없는 제한 된 페이지에 액세스 하려고 액세스할 **또는** 시스템에 의해 인증 된 하지 않은 경우.</span><span class="sxs-lookup"><span data-stu-id="c506c-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="c506c-180">로그인 페이지의 양식을 제출 되 면는 `OnPostAsync` 작업 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="c506c-181">`PasswordSignInAsync` 라고 하는 `_signInManager` 개체 (종속성 주입으로 제공 됨).</span><span class="sxs-lookup"><span data-stu-id="c506c-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="c506c-182">기본 `Controller` 클래스는 컨트롤러의 메서드에서 접근할 수 있는 `User` 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-182">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="c506c-183">예를 들어, 열거할 수 있습니다 `User.Claims` 을 권한 부여 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="c506c-184">자세한 내용은 <xref:security/authorization/introduction>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c506c-184">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c506c-185">사용자가 선택할 때 로그인 양식이 표시 되는 **로그인** 연결 또는 인증을 요구 하는 페이지에 액세스할 때 자동으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-185">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="c506c-186">사용자가 Login 페이지의 양식을 제출하면 `AccountController`의 `Login` 액션이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-186">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="c506c-187">`Login` 액션은 `_signInManager` 개체의 (종속성 주입으로 `AccountController` 에 제공된) `PasswordSignInAsync`를 호출합니다. </span><span class="sxs-lookup"><span data-stu-id="c506c-187">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="c506c-188">기본 (`Controller` 나 `PageModel`) 클래스는 `User` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-188">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="c506c-189">예를 들어 `User.Claims` 권한 부여 결정을 열거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-189">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="c506c-190">로그 아웃</span><span class="sxs-lookup"><span data-stu-id="c506c-190">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c506c-191">합니다 **로그 아웃** 링크를 호출 하는 `LogoutModel.OnPost` 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-191">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="c506c-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 하는 사용자의 클레임을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span> <span data-ttu-id="c506c-193">호출 후 리디렉션 하지 `SignOutAsync` 또는 사용자에 게 **하지** 로그 아웃 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-193">Don't redirect after calling `SignOutAsync` or the user will **not** be signed out.</span></span>

<span data-ttu-id="c506c-194">게시물에 지정 된 *Pages/Shared/_LoginPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c506c-194">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="c506c-195">**LogOut** 링크를 클릭하면 `LogOut` 액션이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-195">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="c506c-196">이전 호출의 `_signInManager.SignOutAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="c506c-196">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="c506c-197">`SignOutAsync`메서드는 쿠키에 저장된 사용자의 클레임을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-197">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="c506c-198">테스트 Id</span><span class="sxs-lookup"><span data-stu-id="c506c-198">Test Identity</span></span>

<span data-ttu-id="c506c-199">기본 웹 프로젝트 템플릿 홈 페이지에 대 한 익명 액세스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-199">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="c506c-200">Id를 테스트 하려면 추가 [ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 개인 정보 페이지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-200">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="c506c-201">에 로그인 하는 경우 로그 아웃 합니다. 앱을 실행 하 고 선택 합니다 **개인 정보 보호** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-201">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="c506c-202">로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-202">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="c506c-203">Identity를 탐색 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-203">Explore Identity</span></span>

<span data-ttu-id="c506c-204">Identity를 자세히 탐색:</span><span class="sxs-lookup"><span data-stu-id="c506c-204">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="c506c-205">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="c506c-205">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="c506c-206">각 페이지 및 디버거를 단계별로의 소스를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-206">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="c506c-207">Id 구성 요소</span><span class="sxs-lookup"><span data-stu-id="c506c-207">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c506c-208">에 포함 된 모든 Identity 종속 된 NuGet 패키지를 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-208">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="c506c-209">Id에 대 한 기본 패키지가 [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-209">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="c506c-210">ASP.NET Core Identity에 대한 주요 인터페이스 모음을 포함하고 있는 이 패키지는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-210">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="c506c-211">ASP.NET Identity로 마이그레이션하기</span><span class="sxs-lookup"><span data-stu-id="c506c-211">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="c506c-212">자세한 내용 및 기존 Id 저장소에 마이그레이션하는 방법에 대 한 지침을 참조 하세요 [인증 및 Id 마이그레이션](xref:migration/identity)합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-212">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="c506c-213">암호 강도 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-213">Setting password strength</span></span>

<span data-ttu-id="c506c-214">참조 [구성](#pw) 최소 암호 요구 사항을 설정 하는 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="c506c-214">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c506c-215">다음 단계</span><span class="sxs-lookup"><span data-stu-id="c506c-215">Next Steps</span></span>

* [<span data-ttu-id="c506c-216">ID 구성</span><span class="sxs-lookup"><span data-stu-id="c506c-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
