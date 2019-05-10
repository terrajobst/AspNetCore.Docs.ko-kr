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
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>ASP.NET Core에서 Ws-federation을 사용 하 여 사용자 인증

이 자습서에는 사용자가 Active Directory 페더레이션 서비스 (ADFS)을 같은 WS-페더레이션 인증 공급자를 사용 하 여 로그인 하는 방법을 보여 줍니다. 또는 [Azure Active Directory](/azure/active-directory/) (AAD). 에 설명 된 ASP.NET Core 2.0 샘플 앱 사용 [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)합니다.

ASP.NET Core 2.0 앱 WS-페더레이션이 지원 기능을 제공 [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)합니다. 이 구성 요소에서 이식 [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) 하 고 다양 한 해당 구성 요소의 메커니즘을 공유 합니다. 그러나 구성 요소는 몇 가지 중요 한 방법이 다릅니다.

기본적으로 새 미들웨어:

* 원치 않는 로그인을 허용 하지 않습니다. WS-페더레이션 프로토콜의이 기능은 XSRF 공격에 취약 합니다. 그러나와 함께 사용할 수는 `AllowUnsolicitedLogins` 옵션입니다.
* 로그인 메시지에 대 한 모든 폼 게시를 확인 하지 않습니다. 만 요청을 합니다 `CallbackPath` 로그인 기능 확인 됩니다 `CallbackPath` 기본값으로 `/signin-wsfed` 되지만 변경할 수 있습니다 통해 상속 된 [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성은 [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) 클래스입니다. 이 경로 사용 하 여 다른 인증 공급자를 사용 하 여 공유할 수는 [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) 옵션입니다.

## <a name="register-the-app-with-active-directory"></a>Active Directory 앱 등록

### <a name="active-directory-federation-services"></a>Active Directory Federation Services

* 서버를 엽니다 **신뢰 당사자 트러스트 추가 마법사** ADFS 관리 콘솔에서:

![신뢰 당사자 트러스트 추가 마법사: 환영](ws-federation/_static/AdfsAddTrust.png)

* 데이터를 수동으로 입력 하려면 선택 합니다.

![신뢰 당사자 트러스트 추가 마법사: 데이터 원본 선택](ws-federation/_static/AdfsSelectDataSource.png)

* 신뢰 당사자에 대 한 표시 이름을 입력 합니다. 이름을 ASP.NET Core 앱에 중요 하지 않습니다.

* [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 토큰 암호화, 토큰 암호화 인증서를 구성 하지 되므로 지원 되지 않습니다.

![신뢰 당사자 트러스트 추가 마법사: 인증서 구성](ws-federation/_static/AdfsConfigureCert.png)

* 앱의 URL을 사용 하 여 Ws-federation 수동 프로토콜 지원을 사용 하도록 설정 합니다. 앱에는 포트가 올바른지 확인 합니다.

![신뢰 당사자 트러스트 추가 마법사: URL 구성](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> 이 HTTPS URL 이어야 합니다. IIS Express 개발 하는 동안 앱을 호스트 하는 경우 자체 서명 된 인증서를 제공할 수 있습니다. Kestrel에는 인증서를 수동으로 구성을 해야합니다. 참조 된 [Kestrel 설명서](xref:fundamentals/servers/kestrel) 대 한 자세한 내용은 합니다.

* 클릭 **다음** 마법사의 나머지와 **닫기** 끝입니다.

* ASP.NET Core Id 필요는 **이름 ID** 클레임입니다. 하나를 추가 합니다 **클레임 규칙 편집** 대화 상자:

![클레임 규칙 편집](ws-federation/_static/EditClaimRules.png)

* 에 **변환 클레임 규칙 추가 마법사**, 기본값을 그대로 두고 **LDAP 특성을 클레임으로 보내기** 템플릿을 선택 하 고 **다음**합니다. 규칙 매핑을 추가 합니다 **SAM 계정 이름** LDAP 특성을 **이름 ID** 나가는 클레임:

![변환 클레임 규칙 추가 마법사: 클레임 규칙 구성](ws-federation/_static/AddTransformClaimRule.png)

* 클릭 **완료** > **확인** 에 **클레임 규칙 편집** 창입니다.

### <a name="azure-active-directory"></a>Azure Active Directory

* AAD 테 넌 트의 앱 등록 블레이드로 이동 합니다. 클릭 **새 응용 프로그램 등록**:

![Azure Active Directory: 앱 등록](ws-federation/_static/AadNewAppRegistration.png)

* 앱 등록에 대 한 이름을 입력 합니다. 이 ASP.NET Core 앱에 중요 하지 않습니다.
* 으로 앱에서 수신 대기 하는 URL을 입력 합니다 **로그온 URL**:

![Azure Active Directory: 앱 등록 만들기](ws-federation/_static/AadCreateAppRegistration.png)

* 클릭 **끝점** 을 확인 합니다 **페더레이션 메타 데이터 문서** URL입니다. 이 Ws-federation 미들웨어의 `MetadataAddress`:

![Azure Active Directory: 엔드포인트](ws-federation/_static/AadFederationMetadataDocument.png)

* 새 앱 등록으로 이동 합니다. 클릭 **설정을** > **속성** 기록해 합니다 **앱 ID URI**합니다. 이 Ws-federation 미들웨어의 `Wtrealm`:

![Azure Active Directory: 앱 등록 속성](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>ASP.NET Core Id에 대 한 외부 로그인 공급자로 WS-페더레이션 추가

* 종속성을 추가 [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 프로젝트입니다.
* Ws-federation을 추가 `Startup.ConfigureServices`:

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

### <a name="log-in-with-ws-federation"></a>WS-페더레이션 로그인

이동 하 여 앱을 클릭 합니다 **로그인** 탐색 헤더에 링크 합니다. WsFederation 사용 하 여 로그인 하는 옵션이 있습니다. ![로그인 페이지](ws-federation/_static/WsFederationButton.png)

공급자로 ADFS를 사용 하 여 단추 ADFS 로그인 페이지로 리디렉션합니다. ![ADFS 로그인 페이지](ws-federation/_static/AdfsLoginPage.png)

공급자로 Azure Active Directory를 사용 하 여 AAD 로그인 페이지에 단추를 리디렉션합니다. ![AAD 로그인 페이지](ws-federation/_static/AadSignIn.png)

성공적인 로그인을 새 사용자에 대 한 앱의 사용자 등록 페이지로 리디렉션됩니다. ![등록 페이지](ws-federation/_static/Register.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>ASP.NET Core Id 없이 사용 하 여 Ws-federation

Id 없이 Ws-federation 미들웨어를 사용할 수 있습니다. 예를 들어:

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
