---
title: ASP.NET Core Identity 소개
author: rick-anderson
description: ASP.NET Core 앱에서 Id를 사용 합니다. 암호 요구 사항 (RequireDigit, RequiredLength, RequiredUniqueChars 등)을 설정 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 325a61e6038e79b9a0db72c8360a5cbff2c8ddae
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011199"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core Identity 소개

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Id는 ASP.NET Core 앱에 로그인 기능을 추가 하는 멤버 자격 시스템입니다. 사용자는 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 외부 로그인 공급자를 사용할 수 있습니다. 지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.

SQL Server 데이터베이스를 사용 하 여 id를 구성 하 여 사용자 이름, 암호 및 프로필 데이터를 저장할 수 있습니다. 또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([다운로드 방법)](xref:index#how-to-download-a-sample).

이 항목에서는 Id를 사용 하 여 사용자를 등록 하 고 로그인 하 고 로그 아웃 하는 방법에 대해 알아봅니다. Id를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity 및 AddIdentity

[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) 는 ASP.NET Core 2.1에서 도입 되었습니다. 호출은 `AddDefaultIdentity` 다음을 호출 하는 것과 비슷합니다.

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

자세한 내용은 [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>인증을 사용 하 여 웹 앱 만들기

개별 사용자 계정으로 새로운 ASP.NET Core 웹 응용 프로그램 생성하기.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
* **새 ASP.NET Core 웹 응용 프로그램**을 선택합니다. 프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다. **확인**을 클릭합니다.
* ASP.NET Core **웹 응용 프로그램**을 선택한 다음 **인증 변경**을 선택 합니다.
* **개별 사용자 계정을** 선택 하 고 **확인**을 클릭 합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

생성 된 프로젝트는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다. Id Razor 클래스 라이브러리는 `Identity` 영역을 사용 하 여 끝점을 노출 합니다. 예:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>마이그레이션 적용

마이그레이션을 적용 하 여 데이터베이스를 초기화.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>테스트 등록 및 로그인

앱을 실행 하 고 사용자를 등록 합니다. 화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Id 서비스 구성

서비스는에 `ConfigureServices`추가 됩니다. 일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

위의 코드는 기본 옵션 값을 사용 하 여 Id를 구성 합니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.

   [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 id를 사용 하도록 설정 합니다. `UseAuthentication`은 요청 파이프라인에 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 응용 프로그램에서 사용할 수 있게 됩니다.

   `Configure` 메서드에서 `UseAuthentication` 메서드를 호출하면 응용 프로그램에서 Identity가 활성화됩니다. `UseAuthentication`은 요청 파이프라인에 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   이 서비스들은 응용 프로그램에서 [종속성 주입](xref:fundamentals/dependency-injection)을 통해서 사용할 수 있습니다.

   `Configure` 메서드에서 `UseIdentity` 메서드를 호출하면 응용 프로그램에서 Identity가 활성화됩니다. `UseIdentity`는 요청 파이프라인에 쿠키 기반의 인증 [미들웨어](xref:fundamentals/middleware/index)를 추가합니다.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

자세한 내용은 [IdentityOptions 클래스](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.

## <a name="scaffold-register-login-and-logout"></a>스 캐 폴드 Register, Login 및 LogOut

이 섹션에 표시 된 코드를 생성 하려면 [권한 부여 지침을 사용 하 여 스 캐 폴드 id를 Razor 프로젝트로](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이동 합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Register, Login 및 LogOut 파일을 추가 합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다. 그렇지 않으면에 대해 `ApplicationDbContext`올바른 네임 스페이스를 사용 합니다.

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다. PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.

---

### <a name="examine-register"></a>등록 검사

::: moniker range=">= aspnetcore-2.1"

   사용자가 **등록** 링크 `RegisterModel.OnPostAsync` 를 클릭 하면 작업이 호출 됩니다. 사용자는 `_userManager` 개체에 대해 [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 를 사용 하 여 만들어집니다. `_userManager`는 종속성 주입에 의해 제공 됩니다.

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   사용자가 **등록** 링크 `Register` 를 클릭 하면 작업이에서 `AccountController`호출 됩니다. `Register` 액션은 `_userManager` 개체의 (종속성 주입으로 `AccountController`에 제공된) `CreateAsync`를 호출해서 사용자를 생성합니다:

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   사용자가 정상적으로 생성되면 `_signInManager.SignInAsync` 가 호출되어 사용자가 즉시 로그인됩니다.

   **참고:** 등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.

### <a name="log-in"></a>로그인

::: moniker range=">= aspnetcore-2.1"

다음과 같은 경우 로그인 양식이 표시 됩니다.

* **로그인** 링크가 선택 됩니다.
* 사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.

로그인 페이지의 폼이 제출 `OnPostAsync` 되 면 동작이 호출 됩니다. `PasswordSignInAsync`는 종속성 주입에 `_signInManager` 의해 제공 되는 개체에서 호출 됩니다.

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   기본 `Controller` 클래스는 컨트롤러의 메서드에서 접근할 수 있는 `User` 속성을 제공합니다. 예를 들어를 열거 `User.Claims` 하 고 권한 부여 결정을 내릴 수 있습니다. 자세한 내용은 <xref:security/authorization/introduction>을 참조하세요.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

사용자가 로그인 링크를 선택 하거나 인증을 요구 하는 **페이지에 액세스할** 때 리디렉션되는 경우 로그인 양식이 표시 됩니다. 사용자가 Login 페이지의 양식을 제출하면 `AccountController`의 `Login` 액션이 호출됩니다.

`Login` 액션은 `_signInManager` 개체의 (종속성 주입으로 `AccountController` 에 제공된) `PasswordSignInAsync`를 호출합니다.

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

기본 (`Controller` 또는 `PageModel`) 클래스는 속성을 `User` 노출 합니다. 예를 들어 `User.Claims` 를 열거 하 여 권한 부여 결정을 내릴 수 있습니다.

::: moniker-end

### <a name="log-out"></a>로그 아웃

::: moniker range=">= aspnetcore-2.1"

**로그 아웃** 링크는 작업을 `LogoutModel.OnPost` 호출 합니다. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 된 사용자의 클레임을 지웁니다.

Post는 *Pages/Shared/_LoginPartial*에서 지정 됩니다.

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   **LogOut** 링크를 클릭하면 `LogOut` 액션이 호출됩니다.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   위의 코드는 메서드를 `_signInManager.SignOutAsync` 호출 합니다. `SignOutAsync`메서드는 쿠키에 저장된 사용자의 클레임을 삭제합니다.

::: moniker-end

## <a name="test-identity"></a>테스트 Id

기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다. Id를 테스트 하려면 개인 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 정보 페이지에를 추가 합니다.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다. 로그인 페이지로 리디렉션됩니다.

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Id 탐색

Id를 자세히 살펴보려면:

* [전체 id UI 원본 만들기](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.

::: moniker-end

## <a name="identity-components"></a>Id 구성 요소

::: moniker range=">= aspnetcore-2.1"

모든 Id 종속 NuGet 패키지는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 포함 되어 있습니다.

::: moniker-end

Id에 대 한 기본 패키지는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)입니다. ASP.NET Core Identity에 대한 주요 인터페이스 모음을 포함하고 있는 이 패키지는 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함되어 있습니다.

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Identity로 마이그레이션하기

기존 Id 저장소를 마이그레이션하는 방법에 대 한 자세한 내용 및 지침은 [인증 및 Id 마이그레이션](xref:migration/identity)을 참조 하세요.

## <a name="setting-password-strength"></a>암호 강도 설정

최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [ID 구성](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
