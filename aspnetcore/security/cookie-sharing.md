---
title: ASP.NET 앱 간에 공유 인증 쿠키
author: rick-anderson
description: ASP.NET 4.x 및 ASP.NET Core 앱들 간에 인증 쿠키를 공유하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2019
uid: security/cookie-sharing
ms.openlocfilehash: b2f906ac97fe79b2a66a5ab709bcbcb03ab8cc39
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223917"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>ASP.NET 앱 간에 공유 인증 쿠키

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)

함께 동작하는 개별적인 웹 앱들로 웹 사이트가 구성되는 경우가 종종 있습니다. SSO(Single Sign-On) 환경을 제공하기 위해서는 사이트 내의 앱들이 인증 쿠키를 공유해야 합니다. 이런 시나리오를 지원하기 위해 데이터 보호 스택은 Katana 쿠키 인증 및 ASP.NET Core 쿠키 인증 티켓의 공유를 지원합니다.

이 예제들은 다음을 따릅니다.

* 인증 쿠키 이름이 `.AspNet.SharedCookie`라는 공통적인 값으로 설정됩니다.
* 명시적이나 기본적으로 `AuthenticationType`을 `Identity.Application`으로 설정합니다.
* 데이터 보호 시스템이 데이터 보호 키를 공유할 수 있도록 공통 앱 이름(`SharedCookieApp`)을 사용합니다.
* 인증 체계로 `Identity.Application`을 사용합니다. 어떤 체계를 사용하든지 기본 체계로 또는 명시적으로 설정하여 공유 쿠키 *앱 내에서 그리고 앱 간에* 일관되게 사용해야 합니다. 이 체계는 쿠키를 암호화하거나 해독할 때 사용되므로 앱 간에 일관된 체계가 사용되어야 합니다.
* 공통적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치를 사용합니다.
  * ASP.NET Core 앱에서 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 키 저장소 위치를 설정 하는 데 사용 됩니다.
  * 쿠키 인증 미들웨어에서.NET Framework 앱의 구현을 사용 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>합니다. `DataProtectionProvider`는 인증 쿠키 페이로드 데이터의 암호화 및 해독을 위한 데이터 보호 서비스를 제공합니다. `DataProtectionProvider` 인스턴스는 앱의 다른 부분에서 사용되는 데이터 보호 시스템과 격리됩니다. [DataProtectionProvider.Create (System.IO.DirectoryInfo, 작업\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 허용 된 <xref:System.IO.DirectoryInfo> 데이터 보호 키 저장소에 대 한 위치를 지정 합니다.
* `DataProtectionProvider` 필요 합니다 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지:
  * ASP.NET Core 2.x 앱에서 참조 된 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.
  * .NET Framework 앱에 대 한 패키지 참조 추가 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)합니다.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 일반적인 앱 이름을 설정합니다.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>ASP.NET Core 앱 간에 인증 쿠키 공유하기

ASP.NET Core Identity를 사용하는 경우:

* 앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다. 공통 키 저장소 위치를 제공 합니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 다음 예제에서 메서드. 사용 하 여 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 일반적인 공유 앱 이름을 구성 하려면 (`SharedCookieApp` 다음 예제에서). 자세한 내용은 <xref:security/data-protection/configuration/overview>을 참조하세요.
* 사용 된 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 확장 메서드를 쿠키에 대 한 데이터 보호 서비스를 설정 합니다.
* 인증 유형으로 다음 예에서 `Identity.Application` 기본적으로 합니다.

`Startup.ConfigureServices`의 경우

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

데이터 보호 및 인증에 직접 ASP.NET Core Id 없이 쿠키를 사용 하는 경우 구성 `Startup.ConfigureServices`합니다. 인증 유형으로 다음 예에서 `Identity.Application`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

하위 도메인 간에 쿠키를 공유하는 앱을 호스팅할 경우, [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) 속성에 공통 도메인을 지정합니다. `second_subdomain.contoso.com` 및 `contoso.com`과 같은 `first_subdomain.contoso.com`에서 앱 간에 쿠키를 공유하려면 `Cookie.Domain`을 `.contoso.com`으로 지정합니다.

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>미사용 데이터 보호 키는 암호화

프로덕션 배포의 경우 DPAPI 또는 X509Certificate로 미사용 키를 암호화하도록 `DataProtectionProvider`를 구성합니다. 자세한 내용은 <xref:security/data-protection/implementation/key-encryption-at-rest>을 참조하세요. 다음 예제에서는 인증서 지문을 제공 되어 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET 간에 인증 쿠키 공유 4.x 및 ASP.NET Core 앱

ASP.NET Core 쿠키 인증 미들웨어와 호환 되는 인증 쿠키를 생성 하려면 Katana 쿠키 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱을 구성할 수 있습니다. 이렇게 하면 사이트 전체에 걸쳐 원활한 SSO 경험을 제공 하는 동안 여러 단계에서 대규모 사이트의 개별 앱을 업그레이드 합니다.

앱에서 Katana 쿠키 인증 미들웨어를 사용 하는 경우 호출 `UseCookieAuthentication` 프로젝트의 *Startup.Auth.cs* 파일입니다. ASP.NET 4.x 웹 앱 프로젝트는 Visual Studio 2013을 사용 하 여 만들어지고 나중 Katana 쿠키 인증 미들웨어를 사용 하 여 기본적으로. 하지만 `UseCookieAuthentication` 사용 되지 않으며를 호출 하는 ASP.NET Core 앱에 대 한 지원 되지 않는 `UseCookieAuthentication` ASP.NET Katana 쿠키 인증 미들웨어를 사용 하는 4.x 앱의 유효 합니다.

ASP.NET 4.x 앱.NET Framework 4.5.1을 대상 해야 이상. 그렇지 않으면 필요한 NuGet 패키지를 설치하지 못합니다.

에 설명 된 대로 ASP.NET 4.x 앱 및 ASP.NET Core 앱 간에 인증 쿠키를 공유 하려면 ASP.NET Core 앱을 구성 합니다 [ASP.NET Core 앱 간에 인증 쿠키 공유](#share-authentication-cookies-among-aspnet-core-apps) 섹션을 선택한 다음으로 ASP.NET 4.x 앱 구성 다음과 같이 합니다.

앱의 패키지를 최신 릴리스로 업데이트 되도록 확인 합니다. 설치 합니다 [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 각 ASP.NET 4.x 응용 프로그램 패키지입니다.

찾아 수정에 대 한 호출 `UseCookieAuthentication`:

* ASP.NET Core 쿠키 인증 미들웨어에서 사용 하는 이름에 일치 시키려면 쿠키 이름 변경 (`.AspNet.SharedCookie` 예제에서).
* 인증 유형으로 다음 예에서 `Identity.Application`합니다.
* 공통 데이터 보호 키 저장소 위치로 초기화된 `DataProtectionProvider`의 인스턴스를 제공합니다.
* 인증 쿠키를 공유 하는 모든 앱에서 사용 하는 일반 앱 이름과 앱 이름을 설정 되어 있는지 확인 (`SharedCookieApp` 예제에서).

설정 하지 않는 경우 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 하 고 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`설정 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 고유 사용자를 구별 하는 클레임을 합니다.

*App_Start/Startup.Auth.cs*:

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
            DataProtectionProvider.Create({PATH TO COMMON KEY RING FOLDER},
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

인증 유형을 사용자 id를 생성 하는 경우 (`Identity.Application`)에 정의 된 형식과 일치 해야 `AuthenticationType` 집합과 `UseCookieAuthentication` 에서 *App_Start/Startup.Auth.cs*합니다.

*Models/IdentityModels.cs*:

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

앱 스키마 (Id의 동일한 버전), 동일한 사용자 데이터베이스에서 각 앱에 대 한 Id 시스템 가리키고 있는지 확인 하는 동일한 Id를 사용 하는 경우. 그렇지 않으면 신원 시스템이 인증 쿠키의 정보를 데이터베이스의 정보와 비교하려고 시도할 때 런타임에 오류가 발생합니다.

Id 스키마 앱 간에 서로 다른 경우 일반적으로 앱에 다른 버전을 식별, 사용 하 고 있으므로 Id의 최신 버전에 따라 일반적인 데이터베이스를 공유 불가능 다시 매핑 및 다른 앱의 Identity 스키마에서 열을 추가 하지 않고 있습니다. 것이 효율적으로 일반적인 데이터베이스 앱에서 공유할 수 있도록 최신 Id 버전을 사용 하는 다른 앱을 업그레이드 합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:host-and-deploy/web-farm>
