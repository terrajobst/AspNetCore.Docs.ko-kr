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
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="20caa-103">ASP.NET 앱 간에 공유 인증 쿠키</span><span class="sxs-lookup"><span data-stu-id="20caa-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="20caa-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="20caa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="20caa-105">함께 동작하는 개별적인 웹 앱들로 웹 사이트가 구성되는 경우가 종종 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="20caa-106">SSO(Single Sign-On) 환경을 제공하기 위해서는 사이트 내의 앱들이 인증 쿠키를 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="20caa-107">이런 시나리오를 지원하기 위해 데이터 보호 스택은 Katana 쿠키 인증 및 ASP.NET Core 쿠키 인증 티켓의 공유를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="20caa-108">이 예제들은 다음을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-108">In the examples that follow:</span></span>

* <span data-ttu-id="20caa-109">인증 쿠키 이름이 `.AspNet.SharedCookie`라는 공통적인 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="20caa-110">명시적이나 기본적으로 `AuthenticationType`을 `Identity.Application`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="20caa-111">데이터 보호 시스템이 데이터 보호 키를 공유할 수 있도록 공통 앱 이름(`SharedCookieApp`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="20caa-112">인증 체계로 `Identity.Application`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="20caa-113">어떤 체계를 사용하든지 기본 체계로 또는 명시적으로 설정하여 공유 쿠키 *앱 내에서 그리고 앱 간에* 일관되게 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="20caa-114">이 체계는 쿠키를 암호화하거나 해독할 때 사용되므로 앱 간에 일관된 체계가 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="20caa-115">공통적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="20caa-116">ASP.NET Core 앱에서 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 키 저장소 위치를 설정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="20caa-117">쿠키 인증 미들웨어에서.NET Framework 앱의 구현을 사용 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="20caa-118">`DataProtectionProvider`는 인증 쿠키 페이로드 데이터의 암호화 및 해독을 위한 데이터 보호 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="20caa-119">`DataProtectionProvider` 인스턴스는 앱의 다른 부분에서 사용되는 데이터 보호 시스템과 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="20caa-120">[DataProtectionProvider.Create (System.IO.DirectoryInfo, 작업\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 허용 된 <xref:System.IO.DirectoryInfo> 데이터 보호 키 저장소에 대 한 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="20caa-121">`DataProtectionProvider` 필요 합니다 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지:</span><span class="sxs-lookup"><span data-stu-id="20caa-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="20caa-122">ASP.NET Core 2.x 앱에서 참조 된 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="20caa-123">.NET Framework 앱에 대 한 패키지 참조 추가 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="20caa-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 일반적인 앱 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="20caa-125">ASP.NET Core 앱 간에 인증 쿠키 공유하기</span><span class="sxs-lookup"><span data-stu-id="20caa-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="20caa-126">ASP.NET Core Identity를 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="20caa-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="20caa-127">앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="20caa-128">공통 키 저장소 위치를 제공 합니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 다음 예제에서 메서드.</span><span class="sxs-lookup"><span data-stu-id="20caa-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="20caa-129">사용 하 여 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 일반적인 공유 앱 이름을 구성 하려면 (`SharedCookieApp` 다음 예제에서).</span><span class="sxs-lookup"><span data-stu-id="20caa-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="20caa-130">자세한 내용은 <xref:security/data-protection/configuration/overview>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="20caa-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="20caa-131">사용 된 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 확장 메서드를 쿠키에 대 한 데이터 보호 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="20caa-132">인증 유형으로 다음 예에서 `Identity.Application` 기본적으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-132">In the following example, the authentication type is set to `Identity.Application` by default.</span></span>

<span data-ttu-id="20caa-133">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="20caa-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="20caa-134">데이터 보호 및 인증에 직접 ASP.NET Core Id 없이 쿠키를 사용 하는 경우 구성 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="20caa-135">인증 유형으로 다음 예에서 `Identity.Application`:</span><span class="sxs-lookup"><span data-stu-id="20caa-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="20caa-136">하위 도메인 간에 쿠키를 공유하는 앱을 호스팅할 경우, [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) 속성에 공통 도메인을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="20caa-137">`second_subdomain.contoso.com` 및 `contoso.com`과 같은 `first_subdomain.contoso.com`에서 앱 간에 쿠키를 공유하려면 `Cookie.Domain`을 `.contoso.com`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="20caa-138">미사용 데이터 보호 키는 암호화</span><span class="sxs-lookup"><span data-stu-id="20caa-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="20caa-139">프로덕션 배포의 경우 DPAPI 또는 X509Certificate로 미사용 키를 암호화하도록 `DataProtectionProvider`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="20caa-140">자세한 내용은 <xref:security/data-protection/implementation/key-encryption-at-rest>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="20caa-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="20caa-141">다음 예제에서는 인증서 지문을 제공 되어 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span><span class="sxs-lookup"><span data-stu-id="20caa-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="20caa-142">ASP.NET 간에 인증 쿠키 공유 4.x 및 ASP.NET Core 앱</span><span class="sxs-lookup"><span data-stu-id="20caa-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="20caa-143">ASP.NET Core 쿠키 인증 미들웨어와 호환 되는 인증 쿠키를 생성 하려면 Katana 쿠키 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="20caa-144">이렇게 하면 사이트 전체에 걸쳐 원활한 SSO 경험을 제공 하는 동안 여러 단계에서 대규모 사이트의 개별 앱을 업그레이드 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="20caa-145">앱에서 Katana 쿠키 인증 미들웨어를 사용 하는 경우 호출 `UseCookieAuthentication` 프로젝트의 *Startup.Auth.cs* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="20caa-146">ASP.NET 4.x 웹 앱 프로젝트는 Visual Studio 2013을 사용 하 여 만들어지고 나중 Katana 쿠키 인증 미들웨어를 사용 하 여 기본적으로.</span><span class="sxs-lookup"><span data-stu-id="20caa-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="20caa-147">하지만 `UseCookieAuthentication` 사용 되지 않으며를 호출 하는 ASP.NET Core 앱에 대 한 지원 되지 않는 `UseCookieAuthentication` ASP.NET Katana 쿠키 인증 미들웨어를 사용 하는 4.x 앱의 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="20caa-148">ASP.NET 4.x 앱.NET Framework 4.5.1을 대상 해야 이상.</span><span class="sxs-lookup"><span data-stu-id="20caa-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="20caa-149">그렇지 않으면 필요한 NuGet 패키지를 설치하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="20caa-150">에 설명 된 대로 ASP.NET 4.x 앱 및 ASP.NET Core 앱 간에 인증 쿠키를 공유 하려면 ASP.NET Core 앱을 구성 합니다 [ASP.NET Core 앱 간에 인증 쿠키 공유](#share-authentication-cookies-among-aspnet-core-apps) 섹션을 선택한 다음으로 ASP.NET 4.x 앱 구성 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="20caa-151">앱의 패키지를 최신 릴리스로 업데이트 되도록 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="20caa-152">설치 합니다 [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 각 ASP.NET 4.x 응용 프로그램 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="20caa-153">찾아 수정에 대 한 호출 `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="20caa-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="20caa-154">ASP.NET Core 쿠키 인증 미들웨어에서 사용 하는 이름에 일치 시키려면 쿠키 이름 변경 (`.AspNet.SharedCookie` 예제에서).</span><span class="sxs-lookup"><span data-stu-id="20caa-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="20caa-155">인증 유형으로 다음 예에서 `Identity.Application`합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="20caa-156">공통 데이터 보호 키 저장소 위치로 초기화된 `DataProtectionProvider`의 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="20caa-157">인증 쿠키를 공유 하는 모든 앱에서 사용 하는 일반 앱 이름과 앱 이름을 설정 되어 있는지 확인 (`SharedCookieApp` 예제에서).</span><span class="sxs-lookup"><span data-stu-id="20caa-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="20caa-158">설정 하지 않는 경우 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 하 고 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`설정 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 고유 사용자를 구별 하는 클레임을 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="20caa-159">*App_Start/Startup.Auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="20caa-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="20caa-160">인증 유형을 사용자 id를 생성 하는 경우 (`Identity.Application`)에 정의 된 형식과 일치 해야 `AuthenticationType` 집합과 `UseCookieAuthentication` 에서 *App_Start/Startup.Auth.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="20caa-161">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="20caa-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="20caa-162">공통 사용자 데이터베이스 사용하기</span><span class="sxs-lookup"><span data-stu-id="20caa-162">Use a common user database</span></span>

<span data-ttu-id="20caa-163">앱 스키마 (Id의 동일한 버전), 동일한 사용자 데이터베이스에서 각 앱에 대 한 Id 시스템 가리키고 있는지 확인 하는 동일한 Id를 사용 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="20caa-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="20caa-164">그렇지 않으면 신원 시스템이 인증 쿠키의 정보를 데이터베이스의 정보와 비교하려고 시도할 때 런타임에 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="20caa-165">Id 스키마 앱 간에 서로 다른 경우 일반적으로 앱에 다른 버전을 식별, 사용 하 고 있으므로 Id의 최신 버전에 따라 일반적인 데이터베이스를 공유 불가능 다시 매핑 및 다른 앱의 Identity 스키마에서 열을 추가 하지 않고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="20caa-166">것이 효율적으로 일반적인 데이터베이스 앱에서 공유할 수 있도록 최신 Id 버전을 사용 하는 다른 앱을 업그레이드 합니다.</span><span class="sxs-lookup"><span data-stu-id="20caa-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20caa-167">추가 자료</span><span class="sxs-lookup"><span data-stu-id="20caa-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
