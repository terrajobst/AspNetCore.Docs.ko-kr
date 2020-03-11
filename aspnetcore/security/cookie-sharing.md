---
title: ASP.NET apps 간에 인증 쿠키 공유
author: rick-anderson
description: ASP.NET 4.x 및 ASP.NET Core 앱들 간에 인증 쿠키를 공유하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7e29be22717f0b97fc115ac036cc54e333bed4e2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651609"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>ASP.NET apps 간에 인증 쿠키 공유

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

함께 동작하는 개별적인 웹 앱들로 웹 사이트가 구성되는 경우가 종종 있습니다. SSO(Single Sign-On) 환경을 제공하기 위해서는 사이트 내의 앱들이 인증 쿠키를 공유해야 합니다. 이런 시나리오를 지원하기 위해 데이터 보호 스택은 Katana 쿠키 인증 및 ASP.NET Core 쿠키 인증 티켓의 공유를 지원합니다.

이 예제들은 다음을 따릅니다.

* 인증 쿠키 이름이 `.AspNet.SharedCookie`일반 값으로 설정 되어 있습니다.
* `AuthenticationType`는 명시적으로 또는 기본적으로 `Identity.Application`로 설정 됩니다.
* 공통 앱 이름은 데이터 보호 시스템에서 데이터 보호 키 (`SharedCookieApp`)를 공유 하도록 설정 하는 데 사용 됩니다.
* `Identity.Application`는 인증 체계로 사용 됩니다. 사용 되는 스키마는 모두 공유 쿠키 앱 *내에서* 일관 되 게 사용 되어야 하며, 기본 스키마로 설정 하거나 명시적으로 설정 하 여 사용 해야 합니다. 이 체계는 쿠키를 암호화하거나 해독할 때 사용되므로 앱 간에 일관된 체계가 사용되어야 합니다.
* 일반적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치가 사용 됩니다.
  * ASP.NET Core apps에서 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>를 사용 하 여 키 저장소 위치를 설정 합니다.
  * .NET Framework apps에서 쿠키 인증 미들웨어는 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>구현을 사용 합니다. `DataProtectionProvider`는 인증 쿠키 페이로드 데이터의 암호화 및 암호 해독을 위한 데이터 보호 서비스를 제공 합니다. `DataProtectionProvider` 인스턴스는 응용 프로그램의 다른 부분에서 사용 되는 데이터 보호 시스템에서 격리 됩니다. [DataProtectionProvider (system.io.directoryinfo, Action\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 는 데이터 보호 키 저장소의 위치를 지정 하는 <xref:System.IO.DirectoryInfo>을 허용 합니다.
* `DataProtectionProvider`에는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지가 필요 합니다.
  * ASP.NET Core 2.x 앱에서 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조 합니다.
  * .NET Framework apps에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)에 대 한 패키지 참조를 추가 합니다.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>는 일반적인 앱 이름을 설정 합니다.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>ASP.NET Core Id와 인증 쿠키 공유

ASP.NET Core Identity를 사용하는 경우:

* 앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다. 다음 예에서는 일반적인 키 저장소 위치가 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 메서드에 제공 됩니다. 다음 예에서는 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>를 사용 하 여 공통 공유 앱 이름 (`SharedCookieApp`를 구성 합니다. 자세한 내용은 <xref:security/data-protection/configuration/overview>을 참조하세요.
* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 확장 메서드를 사용 하 여 쿠키에 대 한 데이터 보호 서비스를 설정 합니다.
* 기본 인증 유형이 `Identity.Application`입니다.

`Startup.ConfigureServices`의 경우:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>ASP.NET Core Id 없이 인증 쿠키 공유

ASP.NET Core Id 없이 쿠키를 직접 사용 하는 경우 `Startup.ConfigureServices`에서 데이터 보호 및 인증을 구성 합니다. 다음 예제에서는 인증 형식이 `Identity.Application`로 설정 됩니다.

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>여러 기본 경로 간에 쿠키 공유

인증 쿠키는 기본 [HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [경로](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)를 사용 합니다. 앱의 쿠키를 다른 기본 경로에서 공유 해야 하는 경우 `Path`를 재정의 해야 합니다.

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>하위 도메인 간에 쿠키 공유

하위 도메인 간에 쿠키를 공유 하는 앱을 호스트 하는 경우에는 [쿠키나 도메인](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) 속성에서 공통 도메인을 지정 합니다. `first_subdomain.contoso.com` 및 `second_subdomain.contoso.com`와 같은 `contoso.com`에서 앱 간에 쿠키를 공유 하려면 `Cookie.Domain`를 `.contoso.com`로 지정 합니다.

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>휴지 상태의 데이터 보호 키 암호화

프로덕션 배포의 경우 DPAPI 또는 X509Certificate를 사용 하 여 미사용 키를 암호화 하도록 `DataProtectionProvider`를 구성 합니다. 자세한 내용은 <xref:security/data-protection/implementation/key-encryption-at-rest>을 참조하세요. 다음 예제에서는 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>에 대 한 인증서 지문을 제공 합니다.

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET 4.x와 ASP.NET Core apps 간에 인증 쿠키 공유

Katana 쿠키 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱은 ASP.NET Core 쿠키 인증 미들웨어와 호환 되는 인증 쿠키를 생성 하도록 구성할 수 있습니다. 이를 통해 여러 단계로 많은 사이트의 개별 앱을 업그레이드할 수 있으며 사이트 전체에서 원활한 SSO 환경을 제공 합니다.

앱이 Katana 쿠키 인증 미들웨어를 사용 하는 경우 프로젝트의 *Startup.Auth.cs* 파일에서 `UseCookieAuthentication`를 호출 합니다. Visual Studio 2013 이상에서 만든 ASP.NET 4.x 웹 앱 프로젝트는 기본적으로 Katana 쿠키 인증 미들웨어를 사용 합니다. `UseCookieAuthentication`은 ASP.NET Core 앱에 대해 사용 되지 않으며 지원 되지 않지만 Katana 쿠키 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱에서 `UseCookieAuthentication`를 호출 하는 것은 유효 합니다.

ASP.NET 4.x 앱은 4.5.1 이상 .NET Framework 대상으로 해야 합니다. 그렇지 않으면 필요한 NuGet 패키지를 설치하지 못합니다.

ASP.NET 4.x 앱과 ASP.NET Core 앱 간에 인증 쿠키를 공유 하려면 [ASP.NET Core 앱 간에 인증 쿠키 공유](#share-authentication-cookies-with-aspnet-core-identity) 섹션에 명시 된 대로 ASP.NET Core 앱을 구성 하 고 다음과 같이 ASP.NET 4.x 앱을 구성 합니다.

앱의 패키지가 최신 릴리스로 업데이트 되는지 확인 합니다. 각 ASP.NET 4.x 앱에 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 패키지를 설치 합니다.

`UseCookieAuthentication`에 대 한 호출을 찾아서 수정 합니다.

* ASP.NET Core 쿠키 인증 미들웨어 (예제에서는`.AspNet.SharedCookie`)에서 사용 하는 이름과 일치 하도록 쿠키 이름을 변경 합니다.
* 다음 예제에서는 인증 형식이 `Identity.Application`로 설정 됩니다.
* Common data protection key storage 위치로 초기화 된 `DataProtectionProvider`의 인스턴스를 제공 합니다.
* 앱 이름이 인증 쿠키를 공유 하는 모든 앱에서 사용 하는 공용 앱 이름으로 설정 되어 있는지 확인 합니다 (예제에서는`SharedCookieApp`).

`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 및 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`를 설정 하지 않은 경우 고유한 사용자를 구별 하는 클레임으로 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier>을 설정 합니다.

*App_Start/startup.auth.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

사용자 id를 생성 하는 경우 인증 유형 (`Identity.Application`)은 *App_Start/startup.auth.cs*`UseCookieAuthentication`에 `AuthenticationType` 집합에 정의 된 유형과 일치 해야 합니다.

*모델/IdentityModels*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>공통 사용자 데이터베이스 사용하기

앱에서 동일한 Id 스키마 (동일한 Id 버전)를 사용 하는 경우 각 앱의 Id 시스템이 동일한 사용자 데이터베이스를 가리키고 있는지 확인 합니다. 그렇지 않으면 신원 시스템이 인증 쿠키의 정보를 데이터베이스의 정보와 비교하려고 시도할 때 런타임에 오류가 발생합니다.

앱 간에 Id 스키마가 다른 경우, 일반적으로 앱에서 서로 다른 Id 버전을 사용 하기 때문에 최신 버전의 Id를 기반으로 하는 공통 데이터베이스를 공유 하는 것은 다른 앱의 Id 스키마에서 열을 다시 매핑 및 추가 하지 않고도 가능 합니다. 앱에서 공통 데이터베이스를 공유할 수 있도록 최신 Id 버전을 사용 하도록 다른 앱을 업그레이드 하는 것이 더 효율적입니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:host-and-deploy/web-farm>
