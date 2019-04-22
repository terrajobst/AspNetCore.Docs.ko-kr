---
title: ASP.NET 및 ASP.NET Core에서 앱 간 쿠키 공유하기
author: rick-anderson
description: ASP.NET 4.x 및 ASP.NET Core 앱들 간에 인증 쿠키를 공유하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7a049ed8787808e228859afc051b8697a6261c21
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068312"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a><span data-ttu-id="0fd6e-103">ASP.NET 및 ASP.NET Core에서 앱 간 쿠키 공유하기</span><span class="sxs-lookup"><span data-stu-id="0fd6e-103">Share cookies among apps with ASP.NET and ASP.NET Core</span></span>

<span data-ttu-id="0fd6e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0fd6e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0fd6e-105">함께 동작하는 개별적인 웹 앱들로 웹 사이트가 구성되는 경우가 종종 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="0fd6e-106">SSO(Single Sign-On) 환경을 제공하기 위해서는 사이트 내의 앱들이 인증 쿠키를 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="0fd6e-107">이런 시나리오를 지원하기 위해 데이터 보호 스택은 Katana 쿠키 인증 및 ASP.NET Core 쿠키 인증 티켓의 공유를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="0fd6e-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0fd6e-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0fd6e-109">본문의 예제에서는 쿠키 인증을 사용하는 세 가지 앱 간의 쿠키 공유를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-109">The sample illustrates cookie sharing across three apps that use cookie authentication:</span></span>

* <span data-ttu-id="0fd6e-110">[ASP.NET Core Identity](xref:security/authentication/identity)를 사용하지 않는 ASP.NET Core 2.0 Razor 페이지 앱</span><span class="sxs-lookup"><span data-stu-id="0fd6e-110">ASP.NET Core 2.0 Razor Pages app without using [ASP.NET Core Identity](xref:security/authentication/identity)</span></span>
* <span data-ttu-id="0fd6e-111">ASP.NET Core Identity를 사용하는 ASP.NET Core 2.0 MVC 앱</span><span class="sxs-lookup"><span data-stu-id="0fd6e-111">ASP.NET Core 2.0 MVC app with ASP.NET Core Identity</span></span>
* <span data-ttu-id="0fd6e-112">ASP.NET Core Identity를 사용하는 ASP.NET Framework 4.6.1 MVC 앱</span><span class="sxs-lookup"><span data-stu-id="0fd6e-112">ASP.NET Framework 4.6.1 MVC app with ASP.NET Identity</span></span>

<span data-ttu-id="0fd6e-113">이 예제들은 다음을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-113">In the examples that follow:</span></span>

* <span data-ttu-id="0fd6e-114">인증 쿠키 이름이 `.AspNet.SharedCookie`라는 공통적인 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-114">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="0fd6e-115">명시적이나 기본적으로 `AuthenticationType`을 `Identity.Application`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-115">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="0fd6e-116">데이터 보호 시스템이 데이터 보호 키를 공유할 수 있도록 공통 앱 이름(`SharedCookieApp`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-116">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="0fd6e-117">인증 체계로 `Identity.Application`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-117">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="0fd6e-118">어떤 체계를 사용하든지 기본 체계로 또는 명시적으로 설정하여 공유 쿠키 *앱 내에서 그리고 앱 간에* 일관되게 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-118">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="0fd6e-119">이 체계는 쿠키를 암호화하거나 해독할 때 사용되므로 앱 간에 일관된 체계가 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-119">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="0fd6e-120">공통적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-120">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span> <span data-ttu-id="0fd6e-121">예제 앱은 솔루션 루트에 위치한 *KeyRing*이라는 폴더를 사용하여 데이터 보호 키를 보관합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-121">The sample app uses a folder named *KeyRing* at the root of the solution to hold the data protection keys.</span></span>
* <span data-ttu-id="0fd6e-122">ASP.NET Core 앱에서는 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)을 사용하여 키 저장소 위치를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-122">In the ASP.NET Core apps, [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) is used to set the key storage location.</span></span> <span data-ttu-id="0fd6e-123">그리고 [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)을 사용하여 공통 공유 응용 프로그램 이름을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-123">[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) is used to configure a common shared app name.</span></span>
* <span data-ttu-id="0fd6e-124">.NET Framework 앱은 쿠키 인증 미들웨어에서 [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)의 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-124">In the .NET Framework app, the cookie authentication middleware uses an implementation of [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider).</span></span> <span data-ttu-id="0fd6e-125">`DataProtectionProvider`는 인증 쿠키 페이로드 데이터의 암호화 및 해독을 위한 데이터 보호 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-125">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="0fd6e-126">`DataProtectionProvider` 인스턴스는 앱의 다른 부분에서 사용되는 데이터 보호 시스템과 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-126">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span>
  * <span data-ttu-id="0fd6e-127">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__)는 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) 를 전달받아 데이터 보호 키 저장소의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-127">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__) accepts a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) to specify the location for data protection key storage.</span></span> <span data-ttu-id="0fd6e-128">예제 앱에서는 *KeyRing* 폴더의 경로를 `DirectoryInfo` 에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-128">The sample app provides the path of the *KeyRing* folder to `DirectoryInfo`.</span></span> <span data-ttu-id="0fd6e-129">[DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) 은 공통 앱 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-129">[DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) sets the common app name.</span></span>
  * <span data-ttu-id="0fd6e-130">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)는 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-130">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package.</span></span> <span data-ttu-id="0fd6e-131">ASP.NET Core 2.1 이상의 앱에서 이 패키지를 가져오려면 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-131">To obtain this package for ASP.NET Core 2.1 and later apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="0fd6e-132">.NET Framework를 대상으로 할 경우 `Microsoft.AspNetCore.DataProtection.Extensions`에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-132">When targeting the .NET Framework, add a package reference to `Microsoft.AspNetCore.DataProtection.Extensions`.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="0fd6e-133">ASP.NET Core 앱 간에 인증 쿠키 공유하기</span><span class="sxs-lookup"><span data-stu-id="0fd6e-133">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="0fd6e-134">ASP.NET Core Identity를 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="0fd6e-134">When using ASP.NET Core Identity:</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0fd6e-135">`ConfigureServices` 메서드에서 [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) 확장 메서드를 사용하여 쿠키에 대한 데이터 보호 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-135">In the `ConfigureServices` method, use the [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) extension method to set up the data protection service for cookies.</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

<span data-ttu-id="0fd6e-136">앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-136">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="0fd6e-137">예제 앱에서 `GetKeyRingDirInfo`는 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 메서드에 공통적인 키 저장소 위치를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-137">In the sample apps, `GetKeyRingDirInfo` returns the common key storage location to the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) method.</span></span> <span data-ttu-id="0fd6e-138">[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)을 사용하여 공통 공유 앱 이름(이 예제에서는 `SharedCookieApp`)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-138">Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) to configure a common shared app name (`SharedCookieApp` in the sample).</span></span> <span data-ttu-id="0fd6e-139">자세한 내용은 [데이터 보호 구성하기](xref:security/data-protection/configuration/overview)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-139">For more information, see [Configuring Data Protection](xref:security/data-protection/configuration/overview).</span></span>

<span data-ttu-id="0fd6e-140">하위 도메인 간에 쿠키를 공유하는 앱을 호스팅할 경우, [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) 속성에 공통 도메인을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-140">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) property.</span></span> <span data-ttu-id="0fd6e-141">`first_subdomain.contoso.com` 및 `second_subdomain.contoso.com`과 같은 `contoso.com`에서 앱 간에 쿠키를 공유하려면 `Cookie.Domain`을 `.contoso.com`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-141">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

<span data-ttu-id="0fd6e-142">[예제 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)에서 *CookieAuthWithIdentity.Core* 프로젝트를 참고하시기 바랍니다([다운로드 방법](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0fd6e-142">See the *CookieAuthWithIdentity.Core* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="0fd6e-143">`Configure` 메서드에서 [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions)를 사용하여 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-143">In the `Configure` method, use the [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) to set up:</span></span>

* <span data-ttu-id="0fd6e-144">쿠키에 대한 데이터 보호 서비스.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-144">The data protection service for cookies.</span></span>
* <span data-ttu-id="0fd6e-145">ASP.NET 4.x와 일치하는 `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-145">The `AuthenticationScheme` to match ASP.NET 4.x.</span></span>

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

<span data-ttu-id="0fd6e-146">쿠키를 직접 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="0fd6e-146">When using cookies directly:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

<span data-ttu-id="0fd6e-147">앱 간에 데이터 보호 키와 앱 이름을 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-147">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="0fd6e-148">예제 앱에서 `GetKeyRingDirInfo`는 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 메서드에 공통적인 키 저장소 위치를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-148">In the sample apps, `GetKeyRingDirInfo` returns the common key storage location to the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) method.</span></span> <span data-ttu-id="0fd6e-149">[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)을 사용하여 공통 공유 앱 이름(이 예제에서는 `SharedCookieApp`)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-149">Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) to configure a common shared app name (`SharedCookieApp` in the sample).</span></span> <span data-ttu-id="0fd6e-150">자세한 내용은 [데이터 보호 구성하기](xref:security/data-protection/configuration/overview)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-150">For more information, see [Configuring Data Protection](xref:security/data-protection/configuration/overview).</span></span>

<span data-ttu-id="0fd6e-151">하위 도메인 간에 쿠키를 공유하는 앱을 호스팅할 경우, [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) 속성에 공통 도메인을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-151">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) property.</span></span> <span data-ttu-id="0fd6e-152">`second_subdomain.contoso.com` 및 `contoso.com`과 같은 `first_subdomain.contoso.com`에서 앱 간에 쿠키를 공유하려면 `Cookie.Domain`을 `.contoso.com`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-152">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

<span data-ttu-id="0fd6e-153">[예제 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)에서 *CookieAuth.Core* 프로젝트를 참고하시기 바랍니다([다운로드 방법](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0fd6e-153">See the *CookieAuth.Core* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

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

## <a name="encrypting-data-protection-keys-at-rest"></a><span data-ttu-id="0fd6e-154">미사용 데이터 보호 키 암호화하기</span><span class="sxs-lookup"><span data-stu-id="0fd6e-154">Encrypting data protection keys at rest</span></span>

<span data-ttu-id="0fd6e-155">프로덕션 배포의 경우 DPAPI 또는 X509Certificate로 미사용 키를 암호화하도록 `DataProtectionProvider`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-155">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="0fd6e-156">자세한 내용은 [미사용 키 암호화하기](xref:security/data-protection/implementation/key-encryption-at-rest)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-156">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

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

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="0fd6e-157">ASP.NET 4.x 응용 프로그램과 ASP.NET Core 응용 프로그램 간 인증 쿠키 공유하기</span><span class="sxs-lookup"><span data-stu-id="0fd6e-157">Sharing authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="0fd6e-158">Katana 쿠키 인증 미들웨어를 사용하는 ASP.NET 4.x 앱은 ASP.NET Core 쿠키 인증 미들웨어와 호환되는 인증 쿠키를 생성하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-158">ASP.NET 4.x apps which use Katana cookie authentication middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="0fd6e-159">이를 활용하면 사이트 전체에 걸친 원활한 SSO 환경을 제공하는 동안 대규모 사이트의 개별 앱을 조금씩 업그레이드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-159">This allows upgrading a large site's individual apps piecemeal while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="0fd6e-160">앱이 Katana 쿠키 인증 미들웨어를 사용하는 경우, 미들웨어가 프로젝트의 *Startup.Auth.cs* 파일에서 `UseCookieAuthentication`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-160">When an app uses Katana cookie authentication middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="0fd6e-161">Visual Studio 2013 이상에서 만든 ASP.NET 4.x 웹 앱 프로젝트는 기본적으로 Katana 쿠키 인증 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-161">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana cookie authentication middleware by default.</span></span> <span data-ttu-id="0fd6e-162">`UseCookieAuthentication`은 더 이상 사용되지 않으며 ASP.NET Core 앱에서 지원되지 않지만 Katana 쿠키 인증 미들웨어를 사용하는 ASP.NET 4.x 앱에서 `UseCookieAuthentication`을 호출하는 것은 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-162">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana cookie authentication middleware is valid.</span></span>

<span data-ttu-id="0fd6e-163">ASP.NET 4.x 앱.NET Framework 4.5.1을 대상 해야 이상.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-163">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="0fd6e-164">그렇지 않으면 필요한 NuGet 패키지를 설치하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-164">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="0fd6e-165">ASP.NET 4.x 앱과 ASP.NET Core 앱 간에 인증 쿠키를 공유하려면, 앞에서 설명한 것처럼 ASP.NET Core 앱을 구성한 다음, 다음 과정에 따라 ASP.NET 4.x 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-165">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated above, then configure the ASP.NET 4.x app by following these steps:</span></span>

1. <span data-ttu-id="0fd6e-166">각 ASP.NET 4.x 앱에 [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-166">Install the package [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) into each ASP.NET 4.x app.</span></span>

2. <span data-ttu-id="0fd6e-167">*Startup.Auth.cs*에서 `UseCookieAuthentication`을 호출하는 부분을 찾아서 다음과 같이 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-167">In *Startup.Auth.cs*, locate the call to `UseCookieAuthentication` and modify it as follows.</span></span> <span data-ttu-id="0fd6e-168">쿠키 이름을 ASP.NET Core 쿠키 인증 미들웨어에서 사용하는 이름과 일치하도록 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-168">Change the cookie name to match the name used by the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="0fd6e-169">공통 데이터 보호 키 저장소 위치로 초기화된 `DataProtectionProvider`의 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-169">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span> <span data-ttu-id="0fd6e-170">앱 이름이 쿠키를 공유하는 모든 앱에서 사용하는 공통 앱 이름, 즉 이 예제에서는 `SharedCookieApp`으로 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-170">Make sure that the app name is set to the common app name used by all apps that share cookies, `SharedCookieApp` in the sample app.</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

<span data-ttu-id="0fd6e-171">[예제 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/)에서 *CookieAuthWithIdentity.NETFramework* 프로젝트를 참고하시기 바랍니다([다운로드 방법](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0fd6e-171">See the *CookieAuthWithIdentity.NETFramework* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="0fd6e-172">사용자의 신원을 생성할 때 인증 형식이 `UseCookieAuthentication`으로 설정한 `AuthenticationType`에 정의된 형식과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-172">When generating a user identity, the authentication type must match the type defined in `AuthenticationType` set with `UseCookieAuthentication`.</span></span>

<span data-ttu-id="0fd6e-173">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="0fd6e-173">*Models/IdentityModels.cs*:</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a><span data-ttu-id="0fd6e-174">공통 사용자 데이터베이스 사용하기</span><span class="sxs-lookup"><span data-stu-id="0fd6e-174">Use a common user database</span></span>

<span data-ttu-id="0fd6e-175">각 앱의 신원 시스템이 동일한 사용자 데이터베이스에서 가리키고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-175">Confirm that the identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="0fd6e-176">그렇지 않으면 신원 시스템이 인증 쿠키의 정보를 데이터베이스의 정보와 비교하려고 시도할 때 런타임에 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="0fd6e-176">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0fd6e-177">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0fd6e-177">Additional resources</span></span>

<xref:host-and-deploy/web-farm>
