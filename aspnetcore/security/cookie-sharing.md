---
title: ASP.NET apps 간에 인증 쿠키 공유
author: rick-anderson
description: ASP.NET 4.x 및 ASP.NET Core 앱들 간에 인증 쿠키를 공유하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: security/cookie-sharing
ms.openlocfilehash: 1650afce5c371d0830bb207618b9c1495f0ce587
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022385"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="f79f5-103">ASP.NET apps 간에 인증 쿠키 공유</span><span class="sxs-lookup"><span data-stu-id="f79f5-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="f79f5-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f79f5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f79f5-105">함께 동작하는 개별적인 웹 앱들로 웹 사이트가 구성되는 경우가 종종 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="f79f5-106">SSO(Single Sign-On) 환경을 제공하기 위해서는 사이트 내의 앱들이 인증 쿠키를 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="f79f5-107">이런 시나리오를 지원하기 위해 데이터 보호 스택은 Katana 쿠키 인증 및 ASP.NET Core 쿠키 인증 티켓의 공유를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="f79f5-108">이 예제들은 다음을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-108">In the examples that follow:</span></span>

* <span data-ttu-id="f79f5-109">인증 쿠키 이름이 `.AspNet.SharedCookie`라는 공통적인 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="f79f5-110">명시적이나 기본적으로 `AuthenticationType`을 `Identity.Application`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="f79f5-111">데이터 보호 시스템이 데이터 보호 키를 공유할 수 있도록 공통 앱 이름(`SharedCookieApp`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="f79f5-112">인증 체계로 `Identity.Application`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="f79f5-113">어떤 체계를 사용하든지 기본 체계로 또는 명시적으로 설정하여 공유 쿠키 *앱 내에서 그리고 앱 간에* 일관되게 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="f79f5-114">이 체계는 쿠키를 암호화하거나 해독할 때 사용되므로 앱 간에 일관된 체계가 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="f79f5-115">공통적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="f79f5-116">ASP.NET Core 앱 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 에서는 키 저장소 위치를 설정 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="f79f5-117">.NET Framework apps에서 쿠키 인증 미들웨어는의 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>구현을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="f79f5-118">`DataProtectionProvider`는 인증 쿠키 페이로드 데이터의 암호화 및 해독을 위한 데이터 보호 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="f79f5-119">`DataProtectionProvider` 인스턴스는 앱의 다른 부분에서 사용되는 데이터 보호 시스템과 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="f79f5-120">[DataProtectionProvider (system.io.directoryinfo, Action\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 는을 <xref:System.IO.DirectoryInfo> 허용 하 여 데이터 보호 키 저장소의 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="f79f5-121">`DataProtectionProvider`[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="f79f5-122">ASP.NET Core 2.x 앱에서 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="f79f5-123">.NET Framework apps에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="f79f5-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>공통 앱 이름을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="f79f5-125">ASP.NET Core 앱 간에 인증 쿠키 공유하기</span><span class="sxs-lookup"><span data-stu-id="f79f5-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="f79f5-126">ASP.NET Core Identity를 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="f79f5-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f79f5-127">앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="f79f5-128">다음 예에서는 일반 키 저장소 위치가 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 메서드에 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="f79f5-129">다음 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 예에서는를 사용 하 여 공통 공유`SharedCookieApp` 앱 이름을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="f79f5-130">자세한 내용은 <xref:security/data-protection/configuration/overview>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f79f5-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="f79f5-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 확장 메서드를 사용 하 여 쿠키에 대 한 데이터 보호 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="f79f5-132">기본 인증 형식은 `Identity.Application`입니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="f79f5-133">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="f79f5-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="f79f5-134">ASP.NET Core Id 없이 쿠키를 직접 사용 하는 경우에서 `Startup.ConfigureServices`데이터 보호 및 인증을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f79f5-135">다음 예제에서는 인증 형식이로 `Identity.Application`설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="f79f5-136">하위 도메인 간에 쿠키를 공유하는 앱을 호스팅할 경우, [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) 속성에 공통 도메인을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="f79f5-137">`second_subdomain.contoso.com` 및 `contoso.com`과 같은 `first_subdomain.contoso.com`에서 앱 간에 쿠키를 공유하려면 `Cookie.Domain`을 `.contoso.com`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="f79f5-138">휴지 상태의 데이터 보호 키 암호화</span><span class="sxs-lookup"><span data-stu-id="f79f5-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="f79f5-139">프로덕션 배포의 경우 DPAPI 또는 X509Certificate로 미사용 키를 암호화하도록 `DataProtectionProvider`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="f79f5-140">자세한 내용은 <xref:security/data-protection/implementation/key-encryption-at-rest>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f79f5-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="f79f5-141">다음 예제에서는에 대 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>한 인증서 지문을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="f79f5-142">ASP.NET 4.x와 ASP.NET Core apps 간에 인증 쿠키 공유</span><span class="sxs-lookup"><span data-stu-id="f79f5-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="f79f5-143">Katana 쿠키 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱은 ASP.NET Core 쿠키 인증 미들웨어와 호환 되는 인증 쿠키를 생성 하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="f79f5-144">이를 통해 여러 단계로 많은 사이트의 개별 앱을 업그레이드할 수 있으며 사이트 전체에서 원활한 SSO 환경을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="f79f5-145">앱에서 Katana 쿠키 인증 미들웨어를 사용 하는 경우 `UseCookieAuthentication` 프로젝트의 *Startup.Auth.cs* 파일에서를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="f79f5-146">Visual Studio 2013 이상에서 만든 ASP.NET 4.x 웹 앱 프로젝트는 기본적으로 Katana 쿠키 인증 미들웨어를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="f79f5-147">는 `UseCookieAuthentication` ASP.NET Core apps에서 사용 되지 않으며 지원 되지 않지만 `UseCookieAuthentication` Katana 쿠키 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱에서를 호출 하는 것은 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="f79f5-148">ASP.NET 4.x 앱은 4.5.1 이상 .NET Framework 대상으로 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="f79f5-149">그렇지 않으면 필요한 NuGet 패키지를 설치하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="f79f5-150">ASP.NET 4.x 앱과 ASP.NET Core 앱 간에 인증 쿠키를 공유 하려면 [ASP.NET Core 앱 간에 인증 쿠키 공유](#share-authentication-cookies-among-aspnet-core-apps) 섹션에 명시 된 대로 ASP.NET Core 앱을 구성 하 고 다음과 같이 ASP.NET 4.x 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="f79f5-151">앱의 패키지가 최신 릴리스로 업데이트 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="f79f5-152">각 ASP.NET 4.x 앱에 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="f79f5-153">호출을 찾아서 수정 합니다 `UseCookieAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="f79f5-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="f79f5-154">쿠키 이름 ASP.NET Core 쿠키 인증 미들웨어에서 사용 하는 이름과 일치 하도록 변경 합니다 (`.AspNet.SharedCookie` 예제에서는).</span><span class="sxs-lookup"><span data-stu-id="f79f5-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="f79f5-155">다음 예제에서는 인증 형식이로 `Identity.Application`설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="f79f5-156">공통 데이터 보호 키 저장소 위치로 초기화된 `DataProtectionProvider`의 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="f79f5-157">앱 이름이 인증 쿠키를 공유 하는 모든 앱에서 사용 하는 공용 앱 이름으로 설정 되어 있는지`SharedCookieApp` 확인 합니다 (이 예제에서는).</span><span class="sxs-lookup"><span data-stu-id="f79f5-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="f79f5-158"><xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 및 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 를설정하지않으면고유한사용자를구별하는클레임으로설정합니다.`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`</span><span class="sxs-lookup"><span data-stu-id="f79f5-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="f79f5-159">*App_Start/시작. 인증*:</span><span class="sxs-lookup"><span data-stu-id="f79f5-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="f79f5-160">사용자 id를 생성 하는 경우 인증 유형 (`Identity.Application`)은 *App_Start/Startup* `UseCookieAuthentication` 에서 set `AuthenticationType` 에 정의 된 유형과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="f79f5-161">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="f79f5-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="f79f5-162">공통 사용자 데이터베이스 사용하기</span><span class="sxs-lookup"><span data-stu-id="f79f5-162">Use a common user database</span></span>

<span data-ttu-id="f79f5-163">앱에서 동일한 Id 스키마 (동일한 Id 버전)를 사용 하는 경우 각 앱의 Id 시스템이 동일한 사용자 데이터베이스를 가리키고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="f79f5-164">그렇지 않으면 신원 시스템이 인증 쿠키의 정보를 데이터베이스의 정보와 비교하려고 시도할 때 런타임에 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="f79f5-165">앱 간에 Id 스키마가 다른 경우, 일반적으로 앱에서 서로 다른 Id 버전을 사용 하기 때문에 최신 버전의 Id를 기반으로 하는 공통 데이터베이스를 공유 하는 것은 다른 앱의 Id 스키마에서 열을 다시 매핑 및 추가 하지 않고도 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="f79f5-166">앱에서 공통 데이터베이스를 공유할 수 있도록 최신 Id 버전을 사용 하도록 다른 앱을 업그레이드 하는 것이 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="f79f5-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f79f5-167">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f79f5-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
