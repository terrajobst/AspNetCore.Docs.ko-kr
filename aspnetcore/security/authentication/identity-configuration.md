---
title: ASP.NET Core Id 구성
author: AdrienTorris
description: ASP.NET Core Id 기본값을 이해 하 고 사용자 지정 값을 사용 하도록 Id 속성을 구성 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 823182bed2cb953e07f9374d135868aeb2be9c60
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652695"
---
# <a name="configure-aspnet-core-identity"></a>ASP.NET Core Id 구성

ASP.NET Core Id는 암호 정책, 잠금 및 쿠키 구성과 같은 설정에 기본값을 사용 합니다. 이러한 설정은 `Startup` 클래스에서 재정의할 수 있습니다.

## <a name="identity-options"></a>Id 옵션

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 클래스는 id 시스템을 구성 하는 데 사용할 수 있는 옵션을 나타냅니다. `AddIdentity` 또는 `AddDefaultIdentity`를 호출한 **후** `IdentityOptions` 설정 해야 합니다.

### <a name="claims-identity"></a>클레임 Id

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) 는 다음 표에 표시 된 속성을 사용 하 여 [Claimsidentityoptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) 를 지정 합니다.

| 속성 | Description | 기본값 |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | 역할 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다. | [ClaimTypes. 역할](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | 보안 스탬프 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | 사용자 식별자 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | 사용자 이름 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>잠금

잠금은 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) 메서드에서 설정 합니다.

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

위의 코드는 `Login` Identity 템플릿을 기반으로 합니다. 

잠금 옵션은 `StartUp.ConfigureServices`에 설정 되어 있습니다.

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

위의 코드는 [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) 를 기본값으로 설정 합니다.

실패 한 액세스 시도 횟수를 다시 설정 하 고 시계를 다시 설정 하는 성공적으로 인증 합니다.

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) 는 테이블에 표시 된 속성을 사용 하 여 [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) 를 지정 합니다.

| 속성 | Description | 기본값 |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | 새 사용자를 잠글 수 있는지 여부를 결정 합니다. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | 잠금이 발생 했을 때 사용자가 잠기는 시간입니다. | 5분 |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | 잠금이 설정 된 경우 사용자가 잠길 때까지 실패 한 액세스 시도 횟수입니다. | 5 |

### <a name="password"></a>암호

기본적으로 Id는 암호에 대문자, 소문자, 숫자 및 영숫자가 아닌 문자를 포함 해야 합니다. 암호의 길이는 6 자 이상 이어야 합니다. [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) 는 `Startup.ConfigureServices`에서 설정할 수 있습니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) 는 표에 표시 된 속성을 사용 하 여 [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) 를 지정 합니다.

::: moniker range=">= aspnetcore-2.0"

| 속성 | Description | 기본값 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | 암호에 0-9 사이의 숫자가 필요 합니다. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | 암호의 최소 길이입니다. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | 암호에 소문자가 필요 합니다. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | 암호에 영숫자가 아닌 문자가 필요 합니다. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | ASP.NET Core 2.0 이상에만 적용 됩니다.<br><br> 암호에 고유한 문자 수가 필요 합니다. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | 암호에 대문자를 사용 해야 합니다. | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| 속성 | Description | 기본값 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | 암호에 0-9 사이의 숫자가 필요 합니다. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | 암호의 최소 길이입니다. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | 암호에 소문자가 필요 합니다. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | 암호에 영숫자가 아닌 문자가 필요 합니다. | `true` |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | 암호에 대문자를 사용 해야 합니다. | `true` |

::: moniker-end

### <a name="sign-in"></a>로그인

다음 코드에서는 설정 `SignIn` 설정 합니다 (기본값).

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) 는 테이블에 표시 된 속성을 사용 하 여 [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) 를 지정 합니다.

| 속성 | Description | 기본값 |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | 로그인 하려면 확인 된 전자 메일이 필요 합니다. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | 로그인 하려면 확인 된 전화 번호가 필요 합니다. | `false` |

### <a name="tokens"></a>토큰

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) 는 테이블에 표시 된 속성을 사용 하 여 [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) 를 지정 합니다.

|                                                        속성                                                         |                                                                                      Description                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       인증자를 사용 하 여 2 단계 로그인의 유효성을 검사 하는 데 사용 되는 `AuthenticatorTokenProvider`를 가져오거나 설정 합니다.                                       |
|       [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     전자 메일 변경 확인 전자 메일에 사용 되는 토큰을 생성 하는 데 사용 되는 `ChangeEmailTokenProvider`를 가져오거나 설정 합니다.                                     |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      전화 번호를 변경할 때 사용 되는 토큰을 생성 하는 데 사용 되는 `ChangePhoneNumberTokenProvider`를 가져오거나 설정 합니다.                                      |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             계정 확인 전자 메일에 사용 되는 토큰을 생성 하는 데 사용 되는 토큰 공급자를 가져오거나 설정 합니다.                                              |
|     [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | 암호 재설정 전자 메일에 사용 되는 토큰을 생성 하는 데 사용 되는 [IUserTwoFactorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) 을 가져오거나 설정 합니다. |
|                    [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                공급자 이름으로 사용 되는 키를 사용 하 여 [사용자 토큰 공급자](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) 를 구성 하는 데 사용 됩니다.                 |

### <a name="user"></a>사용자

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) 는 테이블에 표시 된 속성을 사용 하 여 [useroptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) 를 지정 합니다.

| 속성 | Description | 기본값 |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | 사용자 이름에 허용 되는 문자입니다. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | 각 사용자는 고유한 전자 메일을 사용 해야 합니다. | `false` |

### <a name="cookie-settings"></a>쿠키 설정

`Startup.ConfigureServices`에서 앱의 쿠키를 구성 합니다. `AddIdentity` 또는 `AddDefaultIdentity`를 호출한 **후** [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) 를 호출 해야 합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

자세한 내용은 [은 cookieauthenticationoptions.authenticationtype](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)를 참조 하세요.

## <a name="password-hasher-options"></a>암호 Hasher 옵션

암호 해시에 대 한 옵션을 가져오고 설정 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> 합니다.

| 옵션 | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | 새 암호를 해시할 때 사용 되는 호환성 모드입니다. 기본값은 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>입니다. 해시 된 암호의 첫 번째 바이트 ( *형식 마커*)는 암호를 해시 하는 데 사용 되는 해시 알고리즘의 버전을 지정 합니다. 해시에 대해 암호를 확인 하는 경우 <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> 메서드는 첫 번째 바이트를 기준으로 올바른 알고리즘을 선택 합니다. 클라이언트는 암호를 해시 하는 데 사용 된 알고리즘의 버전에 관계 없이 인증할 수 있습니다. 호환성 모드를 설정 하면 *새 암호*의 해시에 영향을 줍니다. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | PBKDF2를 사용 하 여 암호를 해시할 때 사용 되는 반복 횟수입니다. 이 값은 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>으로 설정 된 경우에만 사용 됩니다. 값은 양의 정수 여야 하며 기본값은 `10000`입니다. |

다음 예에서는 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> `12000`로 설정 됩니다.

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
