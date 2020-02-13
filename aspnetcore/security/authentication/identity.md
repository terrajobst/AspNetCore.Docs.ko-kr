---
title: ASP.NET Core Identity 소개
author: rick-anderson
description: ASP.NET Core 앱에서 Id를 사용 합니다. 암호 요구 사항 (RequireDigit, RequiredLength, RequiredUniqueChars 등)을 설정 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 164ba10c1d1e2a73ebeb8240293a58f158055699
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172537"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core Identity 소개

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Id:

* 는 UI (사용자 인터페이스) 로그인 기능을 지 원하는 API입니다.
* 사용자, 암호, 프로필 데이터, 역할, 클레임, 토큰, 전자 메일 확인 등을 관리 합니다.

사용자는 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 외부 로그인 공급자를 사용할 수 있습니다. 지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.

[Id 소스 코드](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) 는 GitHub에서 사용할 수 있습니다. Id를 [스 캐 폴드](xref:security/authentication/scaffold-identity) 하 고 생성 된 파일을 확인 하 여 id와의 템플릿 상호 작용을 검토 합니다.

Id는 일반적으로 사용자 이름, 암호 및 프로필 데이터를 저장 하기 위해 SQL Server 데이터베이스를 사용 하 여 구성 됩니다. 또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.

이 항목에서는 Id를 사용 하 여 사용자를 등록 하 고 로그인 하 고 로그 아웃 하는 방법에 대해 알아봅니다. Id를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.

[Microsoft id 플랫폼](/azure/active-directory/develop/) 은 다음과 같습니다.

* Azure Active Directory (Azure AD) 개발자 플랫폼의 진화
* ASP.NET Core Id와 관련이 없습니다.

[!INCLUDE[](~/includes/IdentityServer4.md)]

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([다운로드 방법)](xref:index#how-to-download-a-sample)

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>인증을 사용 하 여 웹 앱 만들기

개별 사용자 계정으로 새로운 ASP.NET Core 웹 응용 프로그램 생성하기.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일** > **새** > **프로젝트**를 선택 합니다.
* **새 ASP.NET Core 웹 애플리케이션**을 선택합니다. 프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다. **확인**을 클릭합니다.
* ASP.NET Core **웹 응용 프로그램**을 선택한 다음 **인증 변경**을 선택 합니다.
* **개별 사용자 계정을** 선택 하 고 **확인**을 클릭 합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

이전 명령은 SQLite를 사용 하 여 Razor 웹 앱을 만듭니다. LocalDB를 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

생성 된 프로젝트는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다. Id Razor 클래스 라이브러리는 `Identity` 영역이 있는 끝점을 노출 합니다. 예들 들어 다음과 같습니다.

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>마이그레이션 적용

마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

SQLite를 사용 하는 경우이 단계에서는 마이그레이션이 필요 하지 않습니다. LocalDB의 경우 다음 명령을 실행 합니다.

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>테스트 등록 및 로그인

앱을 실행 하 고 사용자를 등록 합니다. 화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Id 서비스 구성

서비스는 `ConfigureServices`에 추가 됩니다. 일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

위의 강조 표시 된 코드는 기본 옵션 값을 사용 하 여 Id를 구성 합니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>를 호출 하 여 id를 사용 하도록 설정 합니다. `UseAuthentication`는 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

템플릿에서 생성 된 앱은 [권한 부여](xref:security/authorization/secure-data)를 사용 하지 않습니다. 앱이 권한 부여를 추가 하는 것이 올바른 순서로 추가 되도록 `app.UseAuthorization` 포함 됩니다. `UseRouting`, `UseAuthentication`, `UseAuthorization`및 `UseEndpoints`는 앞의 코드에 표시 된 순서 대로 호출 해야 합니다.

`IdentityOptions` 및 `Startup`에 대 한 자세한 내용은 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.

## <a name="scaffold-register-login-and-logout"></a>스 캐 폴드 Register, Login 및 LogOut

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Register, Login 및 LogOut 파일을 추가 합니다. 이 섹션에 표시 된 코드를 생성 하려면 [권한 부여 지침을 사용 하 여 스 캐 폴드 id를 Razor 프로젝트로](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이동 합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다. 그렇지 않으면 `ApplicationDbContext`에 대해 올바른 네임 스페이스를 사용 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell은 세미콜론을 명령 구분 기호로 사용합니다. PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.

스 캐 폴딩 Id에 대 한 자세한 내용은 [스 캐 폴드 identity to a Razor project to authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)항목을 참조 하세요.

---

### <a name="examine-register"></a>등록 검사

사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다. 사용자는 `_userManager` 개체의 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 에 의해 만들어집니다. `_userManager`는 종속성 주입에 의해 제공 됩니다.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

사용자가 성공적으로 만들어진 경우 `_signInManager.SignInAsync`에 대 한 호출을 통해 사용자가 로그인 됩니다.

등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.

### <a name="log-in"></a>로그인

다음과 같은 경우 로그인 양식이 표시 됩니다.

* **로그인** 링크가 선택 됩니다.
* 사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.

로그인 페이지의 양식이 제출 되 면 `OnPostAsync` 작업이 호출 됩니다. `PasswordSignInAsync`는 종속성 주입에 의해 제공 되는 `_signInManager` 개체에서 호출 됩니다.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

기본 `Controller` 클래스는 컨트롤러 메서드에서 액세스할 수 있는 `User` 속성을 노출 합니다. 예를 들어 `User.Claims`를 열거 하 고 권한 부여 결정을 내릴 수 있습니다. 자세한 내용은 <xref:security/authorization/introduction>을 참조하세요.

### <a name="log-out"></a>로그아웃

**로그 아웃** 링크는 `LogoutModel.OnPost` 동작을 호출 합니다. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

위의 코드에서는 브라우저가 새 요청을 수행 하 고 사용자에 대 한 id가 업데이트 되도록 코드 `return RedirectToPage();` 리디렉션이 되어야 합니다.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 된 사용자의 클레임을 지웁니다.

Post는 *Pages/Shared/_LoginPartial*에서 지정 됩니다. cshtml:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>테스트 Id

기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다. Id를 테스트 하려면 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)를 추가 합니다.

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다. 로그인 페이지로 리디렉션됩니다.

### <a name="explore-identity"></a>Id 탐색

Id를 자세히 살펴보려면:

* [전체 id UI 원본 만들기](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.

## <a name="identity-components"></a>Id 구성 요소

모든 Id 종속 NuGet 패키지는 [ASP.NET Core 공유 프레임 워크](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)에 포함 되어 있습니다.

Id에 대 한 기본 패키지는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)입니다. 이 패키지에는 ASP.NET Core Id의 핵심 인터페이스 집합이 포함 되어 있으며 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함 되어 있습니다.

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Identity로 마이그레이션하기

기존 Id 저장소를 마이그레이션하는 방법에 대 한 자세한 내용 및 지침은 [인증 및 Id 마이그레이션](xref:migration/identity)을 참조 하세요.

## <a name="setting-password-strength"></a>암호 강도 설정

최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity 및 AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2.1에서 도입 되었습니다. `AddDefaultIdentity`를 호출 하는 것은 다음을 호출 하는 것과 비슷합니다.

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

자세한 내용은 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.

## <a name="prevent-publish-of-static-identity-assets"></a>정적 Id 자산 게시 방지

정적 Id 자산 (Id UI에 대 한 스타일 시트 및 JavaScript 파일)을 웹 루트에 게시 하지 않으려면 다음 `ResolveStaticWebAssetsInputsDependsOn` 속성을 추가 하 고 `RemoveIdentityAssets` 대상을 앱의 프로젝트 파일에 추가 합니다.

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a>다음 단계

* SQLite를 사용 하 여 Id를 구성 하는 방법에 대해서는 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/5131) 를 참조 하세요.
* [ID 구성](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Id는 ASP.NET Core 앱에 로그인 기능을 추가 하는 멤버 자격 시스템입니다. 사용자는 Id에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 외부 로그인 공급자를 사용할 수 있습니다. 지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.

SQL Server 데이터베이스를 사용 하 여 id를 구성 하 여 사용자 이름, 암호 및 프로필 데이터를 저장할 수 있습니다. 또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([다운로드 방법)](xref:index#how-to-download-a-sample)

이 항목에서는 Id를 사용 하 여 사용자를 등록 하 고 로그인 하 고 로그 아웃 하는 방법에 대해 알아봅니다. Id를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity 및 AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2.1에서 도입 되었습니다. `AddDefaultIdentity`를 호출 하는 것은 다음을 호출 하는 것과 비슷합니다.

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

자세한 내용은 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.

## <a name="create-a-web-app-with-authentication"></a>인증을 사용 하 여 웹 앱 만들기

개별 사용자 계정으로 새로운 ASP.NET Core 웹 응용 프로그램 생성하기.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일** > **새** > **프로젝트**를 선택 합니다.
* **새 ASP.NET Core 웹 애플리케이션**을 선택합니다. 프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다. **확인**을 클릭합니다.
* ASP.NET Core **웹 응용 프로그램**을 선택한 다음 **인증 변경**을 선택 합니다.
* **개별 사용자 계정을** 선택 하 고 **확인**을 클릭 합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

생성 된 프로젝트는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다. Id Razor 클래스 라이브러리는 `Identity` 영역이 있는 끝점을 노출 합니다. 예들 들어 다음과 같습니다.

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>마이그레이션 적용

마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.

```powershell
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>테스트 등록 및 로그인

앱을 실행 하 고 사용자를 등록 합니다. 화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Id 서비스 구성

서비스는 `ConfigureServices`에 추가 됩니다. 일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

위의 코드는 기본 옵션 값을 사용 하 여 Id를 구성 합니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.

[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 id를 사용 하도록 설정 합니다. `UseAuthentication`는 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

자세한 내용은 [IdentityOptions 클래스](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.

## <a name="scaffold-register-login-and-logout"></a>스 캐 폴드 Register, Login 및 LogOut

이 섹션에 표시 된 코드를 생성 하려면 [권한 부여 지침을 사용 하 여 스 캐 폴드 id를 Razor 프로젝트로](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 이동 합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Register, Login 및 LogOut 파일을 추가 합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다. 그렇지 않으면 `ApplicationDbContext`에 대해 올바른 네임 스페이스를 사용 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell은 세미콜론을 명령 구분 기호로 사용합니다. PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.

---

### <a name="examine-register"></a>등록 검사

사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다. 사용자는 `_userManager` 개체의 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 에 의해 만들어집니다. `_userManager`는 종속성 주입에 의해 제공 됩니다.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

사용자가 성공적으로 만들어진 경우 `_signInManager.SignInAsync`에 대 한 호출을 통해 사용자가 로그인 됩니다.

**참고:** 등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.

### <a name="log-in"></a>로그인

다음과 같은 경우 로그인 양식이 표시 됩니다.

* **로그인** 링크가 선택 됩니다.
* 사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.

로그인 페이지의 양식이 제출 되 면 `OnPostAsync` 작업이 호출 됩니다. `PasswordSignInAsync`는 종속성 주입에 의해 제공 되는 `_signInManager` 개체에서 호출 됩니다.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

기본 `Controller` 클래스는 컨트롤러 메서드에서 액세스할 수 있는 `User` 속성을 노출 합니다. 예를 들어 `User.Claims`를 열거 하 고 권한 부여 결정을 내릴 수 있습니다. 자세한 내용은 <xref:security/authorization/introduction>을 참조하세요.

### <a name="log-out"></a>로그아웃

**로그 아웃** 링크는 `LogoutModel.OnPost` 동작을 호출 합니다. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 쿠키에 저장 된 사용자의 클레임을 지웁니다.

Post는 *Pages/Shared/_LoginPartial*에서 지정 됩니다. cshtml:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>테스트 Id

기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다. Id를 테스트 하려면 개인 정보 페이지에 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 를 추가 합니다.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다. 로그인 페이지로 리디렉션됩니다.

### <a name="explore-identity"></a>Id 탐색

Id를 자세히 살펴보려면:

* [전체 id UI 원본 만들기](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.

## <a name="identity-components"></a>Id 구성 요소

모든 Id 종속 NuGet 패키지는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 포함 되어 있습니다.

Id에 대 한 기본 패키지는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)입니다. 이 패키지에는 ASP.NET Core Id의 핵심 인터페이스 집합이 포함 되어 있으며 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`에 포함 되어 있습니다.

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Identity로 마이그레이션하기

기존 Id 저장소를 마이그레이션하는 방법에 대 한 자세한 내용 및 지침은 [인증 및 Id 마이그레이션](xref:migration/identity)을 참조 하세요.

## <a name="setting-password-strength"></a>암호 강도 설정

최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* SQLite를 사용 하 여 Id를 구성 하는 방법에 대해서는 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/5131) 를 참조 하세요.
* [ID 구성](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
