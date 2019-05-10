---
title: ASP.NET Core에서 Ws-federation을 사용 하 여 사용자 인증
author: chlowell
description: 이 자습서에는 ASP.NET Core 앱에서 Ws-federation을 사용 하는 방법을 보여 줍니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: 7967410686da0e59742b721c0154e143bf19ba01
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897570"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="8c44d-103">ASP.NET Core에서 Ws-federation을 사용 하 여 사용자 인증</span><span class="sxs-lookup"><span data-stu-id="8c44d-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="8c44d-104">이 자습서에는 사용자가 Active Directory 페더레이션 서비스 (ADFS)을 같은 WS-페더레이션 인증 공급자를 사용 하 여 로그인 하는 방법을 보여 줍니다. 또는 [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="8c44d-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="8c44d-105">에 설명 된 ASP.NET Core 2.0 샘플 앱 사용 [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="8c44d-106">ASP.NET Core 2.0 앱 WS-페더레이션이 지원 기능을 제공 [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="8c44d-107">이 구성 요소에서 이식 [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) 하 고 다양 한 해당 구성 요소의 메커니즘을 공유 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="8c44d-108">그러나 구성 요소는 몇 가지 중요 한 방법이 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="8c44d-109">기본적으로 새 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="8c44d-109">By default, the new middleware:</span></span>

* <span data-ttu-id="8c44d-110">원치 않는 로그인을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="8c44d-111">WS-페더레이션 프로토콜의이 기능은 XSRF 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="8c44d-112">그러나와 함께 사용할 수는 `AllowUnsolicitedLogins` 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="8c44d-113">로그인 메시지에 대 한 모든 폼 게시를 확인 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="8c44d-114">만 요청을 합니다 `CallbackPath` 로그인 기능 확인 됩니다 `CallbackPath` 기본값으로 `/signin-wsfed` 되지만 변경할 수 있습니다 통해 상속 된 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성은 [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="8c44d-115">이 경로 사용 하 여 다른 인증 공급자를 사용 하 여 공유할 수는 [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="8c44d-116">Active Directory 앱 등록</span><span class="sxs-lookup"><span data-stu-id="8c44d-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="8c44d-117">Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="8c44d-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="8c44d-118">서버를 엽니다 **신뢰 당사자 트러스트 추가 마법사** ADFS 관리 콘솔에서:</span><span class="sxs-lookup"><span data-stu-id="8c44d-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![신뢰 당사자 트러스트 추가 마법사: 환영](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="8c44d-120">데이터를 수동으로 입력 하려면 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-120">Choose to enter data manually:</span></span>

![신뢰 당사자 트러스트 추가 마법사: 데이터 원본 선택](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="8c44d-122">신뢰 당사자에 대 한 표시 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="8c44d-123">이름을 ASP.NET Core 앱에 중요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="8c44d-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 토큰 암호화, 토큰 암호화 인증서를 구성 하지 되므로 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![신뢰 당사자 트러스트 추가 마법사: 인증서 구성](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="8c44d-126">앱의 URL을 사용 하 여 Ws-federation 수동 프로토콜 지원을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="8c44d-127">앱에는 포트가 올바른지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-127">Verify the port is correct for the app:</span></span>

![신뢰 당사자 트러스트 추가 마법사: URL 구성](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="8c44d-129">이 HTTPS URL 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="8c44d-130">IIS Express 개발 하는 동안 앱을 호스트 하는 경우 자체 서명 된 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="8c44d-131">Kestrel에는 인증서를 수동으로 구성을 해야합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="8c44d-132">참조 된 [Kestrel 설명서](xref:fundamentals/servers/kestrel) 대 한 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="8c44d-133">클릭 **다음** 마법사의 나머지와 **닫기** 끝입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="8c44d-134">ASP.NET Core Id 필요는 **이름 ID** 클레임입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="8c44d-135">하나를 추가 합니다 **클레임 규칙 편집** 대화 상자:</span><span class="sxs-lookup"><span data-stu-id="8c44d-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![클레임 규칙 편집](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="8c44d-137">에 **변환 클레임 규칙 추가 마법사**, 기본값을 그대로 두고 **LDAP 특성을 클레임으로 보내기** 템플릿을 선택 하 고 **다음**합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="8c44d-138">규칙 매핑을 추가 합니다 **SAM 계정 이름** LDAP 특성을 **이름 ID** 나가는 클레임:</span><span class="sxs-lookup"><span data-stu-id="8c44d-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![변환 클레임 규칙 추가 마법사: 클레임 규칙 구성](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="8c44d-140">클릭 **완료** > **확인** 에 **클레임 규칙 편집** 창입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="8c44d-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="8c44d-141">Azure Active Directory</span></span>

* <span data-ttu-id="8c44d-142">AAD 테 넌 트의 앱 등록 블레이드로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="8c44d-143">클릭 **새 응용 프로그램 등록**:</span><span class="sxs-lookup"><span data-stu-id="8c44d-143">Click **New application registration**:</span></span>

![Azure Active Directory: 앱 등록](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="8c44d-145">앱 등록에 대 한 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-145">Enter a name for the app registration.</span></span> <span data-ttu-id="8c44d-146">이 ASP.NET Core 앱에 중요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="8c44d-147">으로 앱에서 수신 대기 하는 URL을 입력 합니다 **로그온 URL**:</span><span class="sxs-lookup"><span data-stu-id="8c44d-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: 앱 등록 만들기](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="8c44d-149">클릭 **끝점** 을 확인 합니다 **페더레이션 메타 데이터 문서** URL입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="8c44d-150">이 Ws-federation 미들웨어의 `MetadataAddress`:</span><span class="sxs-lookup"><span data-stu-id="8c44d-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: 엔드포인트](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="8c44d-152">새 앱 등록으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-152">Navigate to the new app registration.</span></span> <span data-ttu-id="8c44d-153">클릭 **설정을** > **속성** 기록해 합니다 **앱 ID URI**합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="8c44d-154">이 Ws-federation 미들웨어의 `Wtrealm`:</span><span class="sxs-lookup"><span data-stu-id="8c44d-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: 앱 등록 속성](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="8c44d-156">ASP.NET Core Id에 대 한 외부 로그인 공급자로 WS-페더레이션 추가</span><span class="sxs-lookup"><span data-stu-id="8c44d-156">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="8c44d-157">종속성을 추가 [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-157">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="8c44d-158">Ws-federation을 추가 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8c44d-158">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddIdentity<IdentityUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddAuthentication()
        .AddWsFederation(options =>
        {
            // MetadataAddress represents the Active Directory instance used to authenticate users.
            options.MetadataAddress = "https://<ADFS FQDN or AAD tenant>/FederationMetadata/2007-06/FederationMetadata.xml";

            // Wtrealm is the app's identifier in the Active Directory instance.
            // For ADFS, use the relying party's identifier, its WS-Federation Passive protocol URL:
            options.Wtrealm = "https://localhost:44307/";

            // For AAD, use the App ID URI from the app registration's Properties blade:
            options.Wtrealm = "https://wsfedsample.onmicrosoft.com/bf0e7e6d-056e-4e37-b9a6-2c36797b9f01";
        });

    services.AddMvc()
     // ...
    ```

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="8c44d-159">WS-페더레이션 로그인</span><span class="sxs-lookup"><span data-stu-id="8c44d-159">Log in with WS-Federation</span></span>

<span data-ttu-id="8c44d-160">이동 하 여 앱을 클릭 합니다 **로그인** 탐색 헤더에 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-160">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="8c44d-161">WsFederation 사용 하 여 로그인 하는 옵션이 있습니다. ![로그인 페이지](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="8c44d-161">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="8c44d-162">공급자로 ADFS를 사용 하 여 단추 ADFS 로그인 페이지로 리디렉션합니다. ![ADFS 로그인 페이지](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="8c44d-162">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="8c44d-163">공급자로 Azure Active Directory를 사용 하 여 AAD 로그인 페이지에 단추를 리디렉션합니다. ![AAD 로그인 페이지](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="8c44d-163">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="8c44d-164">성공적인 로그인을 새 사용자에 대 한 앱의 사용자 등록 페이지로 리디렉션됩니다. ![등록 페이지](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="8c44d-164">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="8c44d-165">ASP.NET Core Id 없이 사용 하 여 Ws-federation</span><span class="sxs-lookup"><span data-stu-id="8c44d-165">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="8c44d-166">Id 없이 Ws-federation 미들웨어를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c44d-166">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="8c44d-167">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="8c44d-167">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = WsFederationDefaults.AuthenticationScheme;
    })
    .AddWsFederation(options =>
    {
        options.Wtrealm = Configuration["wsfed:realm"];
        options.MetadataAddress = Configuration["wsfed:metadata"];
    })
    .AddCookie();
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
        // …
}
```
