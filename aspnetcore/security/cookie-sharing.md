---
title: ASP.NET 및 ASP.NET Core에서 앱 간 쿠키 공유하기
author: rick-anderson
description: ASP.NET 4.x 및 ASP.NET Core 앱들 간에 인증 쿠키를 공유하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: security/cookie-sharing
ms.openlocfilehash: 94fafc91012b5a7e0888a6ebf37f517c129af2ac
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892800"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a>ASP.NET 및 ASP.NET Core에서 앱 간 쿠키 공유하기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)

함께 동작하는 개별적인 웹 앱들로 웹 사이트가 구성되는 경우가 종종 있습니다. SSO(Single Sign-On) 환경을 제공하기 위해서는 사이트 내의 앱들이 인증 쿠키를 공유해야 합니다. 이런 시나리오를 지원하기 위해 데이터 보호 스택은 Katana 쿠키 인증 및 ASP.NET Core 쿠키 인증 티켓의 공유를 지원합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([다운로드 방법](xref:index#how-to-download-a-sample))

본문의 예제에서는 쿠키 인증을 사용하는 세 가지 앱 간의 쿠키 공유를 보여줍니다.

* [ASP.NET Core Identity](xref:security/authentication/identity)를 사용하지 않는 ASP.NET Core 2.0 Razor 페이지 앱
* ASP.NET Core Identity를 사용하는 ASP.NET Core 2.0 MVC 앱
* ASP.NET Core Identity를 사용하는 ASP.NET Framework 4.6.1 MVC 앱

이 예제들은 다음을 따릅니다.

* 인증 쿠키 이름이 `.AspNet.SharedCookie`라는 공통적인 값으로 설정됩니다.
* 명시적이나 기본적으로 `AuthenticationType`을 `Identity.Application`으로 설정합니다.
* 데이터 보호 시스템이 데이터 보호 키를 공유할 수 있도록 공통 앱 이름(`SharedCookieApp`)을 사용합니다.
* 인증 체계로 `Identity.Application`을 사용합니다. 어떤 체계를 사용하든지 기본 체계로 또는 명시적으로 설정하여 공유 쿠키 *앱 내에서 그리고 앱 간에* 일관되게 사용해야 합니다. 이 체계는 쿠키를 암호화하거나 해독할 때 사용되므로 앱 간에 일관된 체계가 사용되어야 합니다.
* 공통적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치를 사용합니다. 예제 앱은 솔루션 루트에 위치한 *KeyRing*이라는 폴더를 사용하여 데이터 보호 키를 보관합니다.
* ASP.NET Core 앱에서는 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)을 사용하여 키 저장소 위치를 설정합니다. 그리고 [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)을 사용하여 공통 공유 응용 프로그램 이름을 구성합니다.
* .NET Framework 앱은 쿠키 인증 미들웨어에서 [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)의 구현을 사용합니다. `DataProtectionProvider`는 인증 쿠키 페이로드 데이터의 암호화 및 해독을 위한 데이터 보호 서비스를 제공합니다. `DataProtectionProvider` 인스턴스는 앱의 다른 부분에서 사용되는 데이터 보호 시스템과 격리됩니다.
  * [DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__)는 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) 를 전달받아 데이터 보호 키 저장소의 위치를 지정합니다. 예제 앱에서는 *KeyRing* 폴더의 경로를 `DirectoryInfo` 에 제공합니다. [DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) 은 공통 앱 이름을 설정합니다.
  * [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)는 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지를 필요로 합니다. ASP.NET Core 2.1 이상의 앱에서 이 패키지를 가져오려면 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)를 참조합니다. .NET Framework를 대상으로 할 경우 `Microsoft.AspNetCore.DataProtection.Extensions`에 대한 패키지 참조를 추가합니다.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>ASP.NET Core 앱 간에 인증 쿠키 공유하기

ASP.NET Core Identity를 사용하는 경우:

::: moniker range=">= aspnetcore-2.0"

`ConfigureServices` 메서드에서 [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) 확장 메서드를 사용하여 쿠키에 대한 데이터 보호 서비스를 설정합니다.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다. 예제 앱에서 `GetKeyRingDirInfo`는 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 메서드에 공통적인 키 저장소 위치를 반환합니다. [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)을 사용하여 공통 공유 앱 이름(이 예제에서는 `SharedCookieApp`)을 구성합니다. 자세한 내용은 [데이터 보호 구성하기](xref:security/data-protection/configuration/overview)를 참고하시기 바랍니다.

하위 도메인 간에 쿠키를 공유하는 앱을 호스팅할 경우, [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) 속성에 공통 도메인을 지정합니다. `first_subdomain.contoso.com` 및 `second_subdomain.contoso.com`과 같은 `contoso.com`에서 앱 간에 쿠키를 공유하려면 `Cookie.Domain`을 `.contoso.com`으로 지정합니다.

```csharp
options.Cookie.Domain = ".contoso.com";
```

[예제 코드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)에서 *CookieAuthWithIdentity.Core* 프로젝트를 참고하시기 바랍니다([다운로드 방법](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

`Configure` 메서드에서 [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions)를 사용하여 다음을 설정합니다.

* 쿠키에 대한 데이터 보호 서비스.
* ASP.NET 4.x와 일치하는 `AuthenticationScheme`.

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
{
    options.Cookies.ApplicationCookie.AuthenticationScheme = 
        "ApplicationCookie";

    var protectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"));

    options.Cookies.ApplicationCookie.DataProtectionProvider = 
        protectionProvider;

    options.Cookies.ApplicationCookie.TicketDataFormat = 
        new TicketDataFormat(protectionProvider.CreateProtector(
            "Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", 
            "Cookies", 
            "v2"));
});
```

::: moniker-end

쿠키를 직접 사용하는 경우:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다. 예제 앱에서 `GetKeyRingDirInfo`는 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 메서드에 공통적인 키 저장소 위치를 반환합니다. [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)을 사용하여 공통 공유 앱 이름(이 예제에서는 `SharedCookieApp`)을 구성합니다. 자세한 내용은 [데이터 보호 구성하기](xref:security/data-protection/configuration/overview)를 참고하시기 바랍니다.

하위 도메인 간에 쿠키를 공유하는 앱을 호스팅할 경우, [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) 속성에 공통 도메인을 지정합니다. `second_subdomain.contoso.com` 및 `contoso.com`과 같은 `first_subdomain.contoso.com`에서 앱 간에 쿠키를 공유하려면 `Cookie.Domain`을 `.contoso.com`으로 지정합니다.

```csharp
options.Cookie.Domain = ".contoso.com";
```

[예제 코드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)에서 *CookieAuth.Core* 프로젝트를 참고하시기 바랍니다([다운로드 방법](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
});
```

::: moniker-end

## <a name="encrypting-data-protection-keys-at-rest"></a>미사용 데이터 보호 키 암호화하기

프로덕션 배포의 경우 DPAPI 또는 X509Certificate로 미사용 키를 암호화하도록 `DataProtectionProvider`를 구성합니다. 자세한 내용은 [미사용 키 암호화하기](xref:security/data-protection/implementation/key-encryption-at-rest)를 참고하시기 바랍니다.

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("thumbprint");
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = DataProtectionProvider.Create(
        new DirectoryInfo(@"PATH_TO_KEY_RING"),
        configure =>
        {
            configure.ProtectKeysWithCertificate("thumbprint");
        })
});
```

::: moniker-end

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET 4.x 응용 프로그램과 ASP.NET Core 응용 프로그램 간 인증 쿠키 공유하기

Katana 쿠키 인증 미들웨어를 사용하는 ASP.NET 4.x 앱은 ASP.NET Core 쿠키 인증 미들웨어와 호환되는 인증 쿠키를 생성하도록 구성할 수 있습니다. 이를 활용하면 사이트 전체에 걸친 원활한 SSO 환경을 제공하는 동안 대규모 사이트의 개별 앱을 조금씩 업그레이드할 수 있습니다.

앱이 Katana 쿠키 인증 미들웨어를 사용하는 경우, 미들웨어가 프로젝트의 *Startup.Auth.cs* 파일에서 `UseCookieAuthentication`을 호출합니다. Visual Studio 2013 이상에서 만든 ASP.NET 4.x 웹 앱 프로젝트는 기본적으로 Katana 쿠키 인증 미들웨어를 사용합니다. `UseCookieAuthentication`은 더 이상 사용되지 않으며 ASP.NET Core 앱에서 지원되지 않지만 Katana 쿠키 인증 미들웨어를 사용하는 ASP.NET 4.x 앱에서 `UseCookieAuthentication`을 호출하는 것은 유효합니다.

ASP.NET 4.x 앱.NET Framework 4.5.1을 대상 해야 이상. 그렇지 않으면 필요한 NuGet 패키지를 설치하지 못합니다.

ASP.NET 4.x 앱과 ASP.NET Core 앱 간에 인증 쿠키를 공유하려면, 앞에서 설명한 것처럼 ASP.NET Core 앱을 구성한 다음, 다음 과정에 따라 ASP.NET 4.x 앱을 구성합니다.

1. 각 ASP.NET 4.x 앱에 [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 패키지를 설치합니다.

2. *Startup.Auth.cs*에서 `UseCookieAuthentication`을 호출하는 부분을 찾아서 다음과 같이 수정합니다. 쿠키 이름을 ASP.NET Core 쿠키 인증 미들웨어에서 사용하는 이름과 일치하도록 변경합니다. 공통 데이터 보호 키 저장소 위치로 초기화된 `DataProtectionProvider`의 인스턴스를 제공합니다. 앱 이름이 쿠키를 공유하는 모든 앱에서 사용하는 공통 앱 이름, 즉 이 예제에서는 `SharedCookieApp`으로 설정되어 있는지 확인합니다.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

[예제 코드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)에서 *CookieAuthWithIdentity.NETFramework* 프로젝트를 참고하시기 바랍니다([다운로드 방법](xref:index#how-to-download-a-sample)).

사용자의 신원을 생성할 때 인증 형식이 `UseCookieAuthentication`으로 설정한 `AuthenticationType`에 정의된 형식과 일치해야 합니다.

*Models/IdentityModels.cs*:

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a>공통 사용자 데이터베이스 사용하기

각 앱의 신원 시스템이 동일한 사용자 데이터베이스에서 가리키고 있는지 확인합니다. 그렇지 않으면 신원 시스템이 인증 쿠키의 정보를 데이터베이스의 정보와 비교하려고 시도할 때 런타임에 오류가 발생합니다.

## <a name="additional-resources"></a>추가 자료

<xref:host-and-deploy/web-farm>
