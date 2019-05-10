---
title: 추가 클레임 및 ASP.NET Core에서 외부 공급자의에서 토큰을 유지 합니다.
author: guardrex
description: 추가 클레임 및 외부 공급자의에서 토큰을 설정 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 37c7a51217576669bcaed79d4a212e6412aa8945
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897670"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>추가 클레임 및 ASP.NET Core에서 외부 공급자의에서 토큰을 유지 합니다.

[Luke Latham](https://github.com/guardrex)으로

ASP.NET Core 앱은 추가 클레임 및 Facebook, Google, Microsoft 및 Twitter와 같은 외부 인증 공급자의에서 토큰을 설정할 수 있습니다. 각 공급자는 해당 플랫폼에서 사용자에 대 한 다른 정보를 표시 하지만 받아 추가 클레임을 사용자 데이터를 변환 하기 위한 패턴은 동일 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

앱에서 지 원하는 데는 외부 인증 공급자를 결정 합니다. 각 공급자에 대 한 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 자세한 내용은 <xref:security/authentication/social/index>을 참조하세요. 합니다 [샘플 앱](#sample-app-instructions) 사용 하는 [Google 인증 공급자](xref:security/authentication/google-logins)합니다.

## <a name="set-the-client-id-and-client-secret"></a>클라이언트 ID 및 클라이언트 암호를 설정 합니다.

클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱을 사용 하 여 트러스트 관계를 설정 하는 OAuth 인증 공급자입니다. 클라이언트 ID 및 클라이언트 암호 값 만들어집니다 앱에 대 한 외부 인증 공급자에 의해 앱 공급자와 함께 등록 되는 경우. 앱을 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호를 사용 하 여 독립적으로 구성 되어야 합니다. 자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Google 인증](xref:security/authentication/google-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 인증 공급자](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

샘플 앱 클라이언트 ID 및 Google에서 제공 하는 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

## <a name="establish-the-authentication-scope"></a>인증 범위를 설정 합니다.

목록을 지정 하 여 공급자에서 검색 하는 권한이 지정 된 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>합니다. 일반적인 외부 공급자의 인증 범위는 다음 표에 표시 합니다.

| 공급자  | 범위                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/plus.login`                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Google을 추가 하는 샘플 앱 `plus.login` 권한에서 Google + 기호를 요청 하는 범위:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

## <a name="map-user-data-keys-and-create-claims"></a>사용자 데이터 키를 매핑하고 클레임 만들기

공급자의 옵션을 지정을 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> 외부 공급자의 로그인에 읽기 앱 id에 대 한 사용자 데이터를 JSON 각 키에 대 한 합니다. 클레임 형식에 대 한 자세한 내용은 참조 하세요. <xref:System.Security.Claims.ClaimTypes>합니다.

샘플 앱을 만듭니다는 <xref:System.Security.Claims.ClaimTypes.Gender> 에서 클레임을 `gender` Google 사용자 데이터의 키:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

<xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`)가 사용 하 여 앱에 로그인 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>합니다. 로그인 프로세스 중를 <xref:Microsoft.AspNetCore.Identity.UserManager%601> 저장할 수는 `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>합니다.

샘플 앱에서 `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) 설정 된 <xref:System.Security.Claims.ClaimTypes.Gender> 서명 된에 대 한 클레임에서 `ApplicationUser`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

## <a name="save-the-access-token"></a>액세스 토큰 저장

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 에 대 한 액세스 및 새로 고침 토큰을 저장할지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 성공적인 권한 부여 후 합니다. `SaveTokens` 로 설정 된 `false` 최종 인증 쿠키의 크기를 줄이기 위해 기본적으로 합니다.

값을 설정 하는 샘플 앱 `SaveTokens` 하 `true` 에서 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

때 `OnPostConfirmationAsync` 실행 액세스 토큰을 저장 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))를 외부 공급자 로부터 합니다 `ApplicationUser`의 `AuthenticationProperties`합니다.

샘플 앱에서 액세스 토큰을 저장합니다.

* `OnPostConfirmationAsync` &ndash; 새 사용자 등록에 대 한 실행합니다.
* `OnGetCallbackAsync` &ndash; 이전에 등록 된 사용자가 앱에 로그인 하는 경우를 실행 합니다.

*Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

## <a name="how-to-add-additional-custom-tokens"></a>사용자 지정 토큰을 추가 하는 방법

일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 줍니다 `SaveTokens`를 추가 하는 샘플 앱을 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> 현재 <xref:System.DateTime> 에 대 한는 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 의 `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a>샘플 앱 지침

샘플 앱을 보여 줍니다. 방법:

* Google에서 사용자의 성별을 가져오고 값을 사용 하 여 성별 클레임을 저장 합니다.
* 사용자의 Google 액세스 토큰을 저장할 `AuthenticationProperties`합니다.

샘플 앱을 사용 합니다.

1. 앱을 등록 하 고 유효한 클라이언트 ID 및 Google 인증에 대 한 클라이언트 암호를 가져옵니다. 자세한 내용은 <xref:security/authentication/google-logins>을 참조하세요.
1. 클라이언트 ID 및 앱에 클라이언트 암호를 제공 합니다 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> 의 `Startup.ConfigureServices`합니다.
1. 앱을 실행 하 고 클레임 내 페이지를 요청 합니다. 사용자 서명 되지 않은 경우 앱이 Google에 리디렉션합니다. Google로 로그인 합니다. Google 사용자를 앱으로 다시 리디렉션합니다 (`/Home/MyClaims`). 사용자가 인증 하 고 클레임 내 페이지 로드 됩니다. 성별 클레임은 아래 **사용자 클레임** Google에서 얻은 값. 액세스 토큰에 표시 된 **인증 속성**합니다.

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    b36a7b09-9135-4810-b7a5-78697ff23e99
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    username@gmail.com
AspNet.Identity.SecurityStamp
    29G2TB881ATCUQFJSRFG1S0QJ0OOAWVT
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/gender
    female
http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod
    Google

Authentication Properties

.Token.access_token
    bv42.Dgw...GQMv9ArLPs
.Token.token_type
    Bearer
.Token.expires_at
    2018-08-27T19:08:00.0000000+00:00
.Token.TicketCreated
    8/27/2018 6:08:00 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.issued
    Mon, 27 Aug 2018 18:08:05 GMT
.expires
    Mon, 10 Sep 2018 18:08:05 GMT
```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
