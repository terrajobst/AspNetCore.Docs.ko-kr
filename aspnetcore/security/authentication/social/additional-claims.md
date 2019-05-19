---
title: 추가 클레임 및 ASP.NET Core에서 외부 공급자의에서 토큰을 유지 합니다.
author: guardrex
description: 추가 클레임 및 외부 공급자의에서 토큰을 설정 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: e18287e5a4171b3f7a6daa122111448b8447c1da
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874851"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>추가 클레임 및 ASP.NET Core에서 외부 공급자의에서 토큰을 유지 합니다.

[Luke Latham](https://github.com/guardrex)으로

ASP.NET Core 앱은 추가 클레임 및 Facebook, Google, Microsoft 및 Twitter와 같은 외부 인증 공급자의에서 토큰을 설정할 수 있습니다. 각 공급자는 해당 플랫폼에서 사용자에 대 한 다른 정보를 표시 하지만 받아 추가 클레임을 사용자 데이터를 변환 하기 위한 패턴은 동일 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

앱에서 지 원하는 데는 외부 인증 공급자를 결정 합니다. 각 공급자에 대 한 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 자세한 내용은 <xref:security/authentication/social/index>을 참조하세요. 샘플 앱에서는 합니다 [Google 인증 공급자](xref:security/authentication/google-logins)합니다.

## <a name="set-the-client-id-and-client-secret"></a>클라이언트 ID 및 클라이언트 암호를 설정 합니다.

클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱을 사용 하 여 트러스트 관계를 설정 하는 OAuth 인증 공급자입니다. 클라이언트 ID 및 클라이언트 암호 값 만들어집니다 앱에 대 한 외부 인증 공급자에 의해 앱 공급자와 함께 등록 되는 경우. 앱을 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호를 사용 하 여 독립적으로 구성 되어야 합니다. 자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Google 인증](xref:security/authentication/google-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 인증 공급자](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

샘플 앱 클라이언트 ID 및 Google에서 제공 하는 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>인증 범위를 설정 합니다.

목록을 지정 하 여 공급자에서 검색 하는 권한이 지정 된 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>합니다. 일반적인 외부 공급자의 인증 범위는 다음 표에 표시 합니다.

| 공급자  | 범위                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

샘플 앱에서 Google의 `userinfo.profile` 범위는 프레임 워크에서 자동으로 추가 됩니다. 때 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> 라고 하는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>합니다. 앱 추가 범위에 필요한 경우 옵션에 추가 합니다. 다음 예제에서는 Google에서에서 `https://www.googleapis.com/auth/user.birthday.read` 범위는 사용자의 생일을 검색 하기 위해 추가 됩니다.

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>사용자 데이터 키를 매핑하고 클레임 만들기

공급자의 옵션을 지정 된 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> 또는 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 각 키/하위 키에 대해 외부 공급자의 로그인에 읽기 앱 id에 대 한 사용자 데이터를 JSON입니다. 클레임 형식에 대 한 자세한 내용은 참조 하세요. <xref:System.Security.Claims.ClaimTypes>합니다.

샘플 앱은 로캘 (`urn:google:locale`) 및 사진 (`urn:google:picture`)에서 클레임을 `locale` 및 `picture` Google 사용자 데이터의 키:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`)가 사용 하 여 앱에 로그인 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>합니다. 로그인 프로세스 중를 <xref:Microsoft.AspNetCore.Identity.UserManager%601> 저장할 수는 `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>합니다.

샘플 앱에서 `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) 로캘을 설정 (`urn:google:locale`) 및 사진 (`urn:google:picture`)는 서명에 대 한 클레임에서 `ApplicationUser`에 대 한 클레임을 포함 하 여 <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

기본적으로 사용자의 클레임 인증 쿠키에 저장 됩니다. 인증 쿠키가 너무 큰 경우 앱 때문에 발생할 수 있습니다.

* 브라우저 검색 쿠키 헤더를 너무 깁니다.
* 요청의 전체 크기가 너무 큽니다.

많은 양의 사용자 데이터에 필요한 경우 사용자 요청을 처리 합니다.

* 새로운 앱 하기만을 처리 하는 요청에 대 한 사용자 클레임의 크기와 수를 제한 합니다.
* 사용자 지정을 사용 하 여 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> 쿠키 인증 미들웨어에 대 한 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 에 요청 간에 id를 저장 합니다. 많은 양의 작은 세션 식별자 키를 클라이언트에만 전송 하는 동안 서버에서 id 정보를 유지 합니다.

## <a name="save-the-access-token"></a>액세스 토큰 저장

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 에 대 한 액세스 및 새로 고침 토큰을 저장할지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> 성공적인 권한 부여 후 합니다. `SaveTokens` 로 설정 된 `false` 최종 인증 쿠키의 크기를 줄이기 위해 기본적으로 합니다.

값을 설정 하는 샘플 앱 `SaveTokens` 하 `true` 에서 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

때 `OnPostConfirmationAsync` 실행 액세스 토큰을 저장 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))를 외부 공급자 로부터 합니다 `ApplicationUser`의 `AuthenticationProperties`합니다.

샘플 앱에서 액세스 토큰을 저장 `OnPostConfirmationAsync` (새 사용자 등록) 및 `OnGetCallbackAsync` (이전에 등록 된 사용자)의 *Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>사용자 지정 토큰을 추가 하는 방법

일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 줍니다 `SaveTokens`를 추가 하는 샘플 앱을 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> 현재 <xref:System.DateTime> 에 대 한는 [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) 의 `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a>만들기 및 클레임 추가

프레임 워크는 일반 동작 및 만들고 클레임 컬렉션에 추가 하기 위한 확장 메서드를 제공 합니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.

사용자에서 파생 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> 추상 구현 및 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> 메서드.

자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>을 참조하세요.

## <a name="removal-of-claim-actions-and-claims"></a>클레임 작업 및 클레임 제거

[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 모든 제거 작업에 대 한 클레임을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> 컬렉션에서 합니다. [ClaimActionCollectionMapExtensions.DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 클레임을 삭제 합니다 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id에서입니다. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 주로 사용한 [OIDC (OpenID Connect)](/azure/active-directory/develop/v2-protocols-oidc) 프로토콜에서 생성 된 클레임을 제거 합니다.

## <a name="sample-app-output"></a>예제 응용 프로그램 출력

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="additional-resources"></a>추가 자료

* [엔지니어링 SocialSample 앱 aspnet/AspNetCore](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; 에 연결 된 샘플 앱은 합니다 [aspnet/AspNetCore GitHub 리포지토리의](https://github.com/aspnet/AspNetCore) `master` 엔지니어링 분기 합니다. `master` 분기에 ASP.NET Core의 다음 릴리스에 대 한 현재 개발 중인 코드가 포함 되어 있습니다. ASP.NET Core의 릴리스 버전에 대 한 샘플 앱의 버전을 확인 하려면 사용 합니다 **분기** 드롭다운 목록에 릴리스 분기를 선택 (예를 들어 `release/2.2`).
