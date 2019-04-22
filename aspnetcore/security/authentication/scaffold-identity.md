---
title: ASP.NET Core 프로젝트에서 스 캐 폴드 Id
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Identity를 스 캐 폴드 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 7445db31e461bf61e8a91af7239187a6ece9d011
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705473"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core 프로젝트에서 스 캐 폴드 Id

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2.1 이상에서 제공 [ASP.NET Core Id](xref:security/authentication/identity) 으로 [Razor 클래스 라이브러리](xref:razor-pages/ui-class)합니다. Identity를 포함 하는 응용 프로그램 Identity Razor 클래스 라이브러리 (RCL)에 포함 된 소스 코드를 선택적으로 추가할 스 캐 폴더를 적용할 수 있습니다. 코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다. 예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다. 생성된 코드는 ID RCL의 동일한 코드보다 우선합니다. UI의 모든 권한을 얻고 RCL 기본 사용 하지 않는, 참조 섹션 [전체 id UI 원본 만들기](#full)합니다.

응용 프로그램을 **되지** 포함 인증 RCL Identity 패키지를 추가 하려면 스 캐 폴더를 적용할 수 있습니다. 선택한 ID 코드의 옵션이 생성되어야 합니다.

스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하기 위해 프로젝트를 업데이트 해야 합니다. 이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.

Identity 스 캐 폴더를 실행 하는 경우는 *ScaffoldingReadme.txt* 파일은 프로젝트 디렉터리에 만들어집니다. 합니다 *ScaffoldingReadme.txt* 파일 Identity 스 캐 폴딩 업데이트를 완료 하려면 필요한에 대 한 일반 지침을 포함 합니다. 이 문서에는 보다 자세한 지침이 포함 되어 있습니다.는 *ScaffoldingReadme.txt* 파일입니다.

파일 차이 보여 줍니다. 변경 내용을에서 백업할 수 있습니다 하는 소스 제어 시스템을 사용 하는 것이 좋습니다. Identity 스 캐 폴더를 실행 한 후 변경 내용을 검사 합니다.

> [!NOTE]
> 사용 하는 경우 필요한 서비스를 [2 단계 인증이](xref:security/authentication/identity-enable-qrcodes)를 [계정 확인 및 암호 복구](xref:security/authentication/accconfirm), 및 Id와 기타 보안 기능입니다. Identity를 스 캐 폴딩 하는 경우 서비스 또는 서비스 스텁을 생성 되지 않습니다. 이러한 기능을 사용 하도록 서비스를 수동으로 추가 되어야 합니다. 예를 들어, 참조 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)합니다.

## <a name="scaffold-identity-into-an-empty-project"></a>빈 프로젝트에 스 캐 폴드 identity

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

다음 강조 표시 된 호출을 추가 합니다 `Startup` 클래스:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>기존 권한 부여 없이 Razor 프로젝트에 스 캐 폴드 identity

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

id가 구성 되어 *Areas/Identity/IdentityHostingStartup.cs*합니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)합니다.

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>마이그레이션과 UseAuthentication, 레이아웃

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>인증을 사용 하도록 설정

에 `Configure` 메서드를 `Startup` 클래스를 호출 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 후 `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>레이아웃 변경

선택 사항: 부분 로그인 추가 (`_LoginPartial`) 레이아웃 파일:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>권한 부여를 사용 하 여 Razor 프로젝트에 스 캐 폴드 identity

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
일부 Id 옵션에 구성 된 *Areas/Identity/IdentityHostingStartup.cs*합니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)합니다.

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드 identity

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

선택 사항: 부분 로그인 추가 (`_LoginPartial`)에 *Views/Shared/_Layout.cshtml* 파일:

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* 이동 합니다 *Pages/Shared/_LoginPartial.cshtml* 파일을 *Views/Shared/_LoginPartial.cshtml*

id가 구성 되어 *Areas/Identity/IdentityHostingStartup.cs*합니다. 자세한 내용은 IHostingStartup을 참조 하세요.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

호출 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 후 `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>권한 부여를 사용 하 여 MVC 프로젝트에 스 캐 폴드 identity

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

삭제 된 *페이지/Shared* 폴더 및 해당 폴더의 파일입니다.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>전체 id UI 원본 만들기

Identity UI의 전체 제어를 유지 하려면 Identity 스 캐 폴더를 실행 하 고 선택 **모든 파일 재정의**합니다.

다음 강조 표시 된 코드에는 ASP.NET Core 2.1 웹 앱에서 Id를 가진 기본 Identity UI를 바꾸려면 변경 내용을 보여 줍니다. Identity UI의 모든 권한을 가질이 작업을 수행 하는 것이 좋습니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

다음 코드에서 기본 Identity 바뀝니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

다음 코드 집합을 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)를 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), 및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

등록을 `IEmailSender` 구현 예를 들어:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core 2.1 이상 인증 코드를 변경](xref:migration/20_21#changes-to-authentication-code)
