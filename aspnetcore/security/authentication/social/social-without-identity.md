---
title: Facebook, Google 및 ASP.NET Core Id 없이 외부 공급자 인증
author: rick-anderson
description: Facebook, Google, Twitter, ASP.NET Core Id 없이 등 계정 사용자 인증을 사용 하 여 설명 합니다.
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: e67da513fef1ce453110c465b08e9c7965e71df5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557652"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>ASP.NET Core Id 없이 사용 하 여 소셜 로그인 공급자 인증

<xref:security/authentication/social/index> 사용자가 OAuth 2.0을 사용 하 여 외부 인증 공급자의 자격 증명으로 로그인 하는 방법을 설명 합니다. 해당 항목에서 설명 하는 방식은 인증 공급자를 ASP.NET Core Id를 포함 합니다.

이 샘플에는 외부 인증 공급자를 사용 하는 방법을 보여 줍니다 **없이** ASP.NET Core Id입니다. ASP.NET Core Id의 기능의 일부를 요구 하지 않지만 여전히를 신뢰할 수 있는 외부 인증 공급자를 사용 하 여 통합이 필요 하는 앱에 대 한 유용 합니다.

이 샘플에서는 [Google 인증](xref:security/authentication/google-logins) 사용자를 인증 합니다. Google을 사용 하 여 인증 이동 Google에 로그인 프로세스를 관리 하는 복잡 한 문제입니다. 다른 외부 인증 공급자와 통합 하려면 다음 항목을 참조 합니다.

* [Facebook 인증](xref:security/authentication/facebook-logins)
* [Microsoft 인증](xref:security/authentication/microsoft-logins)
* [Twitter 인증](xref:security/authentication/twitter-logins)
* [기타 공급자](xref:security/authentication/otherlogins)

## <a name="configuration"></a>구성

에 `ConfigureServices` 메서드를 사용 하 여 앱의 인증 체계를 구성 합니다 `AddAuthentication`를 `AddCookie` 및 `AddGoogle` 메서드:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

에 대 한 호출 [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) 앱의 설정 [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)합니다. 합니다 `DefaultScheme` 다음에서 사용 하는 기본 체계 `HttpContext` 인증 확장 메서드:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

앱 설정 `DefaultScheme` 하 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("쿠키") 이러한 확장 메서드에 대 한 기본 체계로 쿠키를 사용 하도록 앱을 구성 합니다. 앱 설정 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> 하 [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google")에 대 한 호출에 대 한 기본 체계로 Google을 사용 하도록 앱 구성 `ChallengeAsync`합니다. `DefaultChallengeScheme`는 `DefaultScheme`를 재정의합니다. 참조 <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> 재정의 하는 추가 속성에 대 한 `DefaultScheme` 설정 된 경우.

에 `Configure` 메서드를 호출 합니다 `UseAuthentication` 설정 하는 인증 미들웨어를 호출 하는 방법을 `HttpContext.User` 속성입니다. 호출 된 `UseAuthentication` 메서드를 호출 하기 전에 `UseMvcWithDefaultRoute` 또는 `UseMvc`:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

인증 체계 및 쿠키 인증에 대 한 자세한 내용은를 참조 하세요. <xref:security/authentication/cookie>합니다.

## <a name="applying-basic-authorization"></a>기본 권한 부여를 적용합니다.

앱의 인증 구성을 적용 하 여 테스트를 `AuthorizeAttribute` 특성을 컨트롤러, 작업 또는 페이지입니다. 다음 코드에 대 한 액세스를 제한 합니다 *개인 정보 보호* 인증 된 사용자에 게 페이지:

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>로그아웃

현재 사용자 로그 아웃을 해당 쿠키를 삭제 하려면 호출 [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0)합니다. 다음 코드를 추가 하는 `Logout` 페이지 처리기를 *인덱스* 페이지:

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

호출 `SignOutAsync` 인증 체계를 지정 하지 않습니다. 앱의 `DefaultScheme` 의 `CookieAuthenticationDefaults.AuthenticationScheme` 를 대신 사용 됩니다.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
