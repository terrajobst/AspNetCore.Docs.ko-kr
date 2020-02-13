---
title: ASP.NET Core Id 없이 쿠키 인증 사용
author: rick-anderson
description: ASP.NET Core Id 없이 쿠키 인증을 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: 62a3d247dade6c83156a8378407d5e3891713fd1
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172112"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>ASP.NET Core Id 없이 쿠키 인증 사용

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Id는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다. 그러나 ASP.NET Core Id가 없는 쿠키 기반 인증 공급자를 사용할 수 있습니다. 자세한 내용은 <xref:security/authentication/identity>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다. **전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다. 사용자는 *Pages/Account/Login. cshtml* 파일의 `AuthenticateUser` 메서드에서 인증 됩니다. 실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.

## <a name="configuration"></a>Configuration

앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지에 대 한 패키지 참조를 만듭니다.

`Startup.ConfigureServices` 메서드에서 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 및 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 메서드를 사용 하 여 인증 미들웨어 서비스를 만듭니다.

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

`AddAuthentication`에 전달 된 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>는 앱에 대 한 기본 인증 체계를 설정 합니다. `AuthenticationScheme`은 쿠키 인증 인스턴스가 여러 개 있고 [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다. `AuthenticationScheme`를 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)로 설정하면 구성표에 "쿠키" 값이 제공됩니다. 체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.

앱의 인증 체계가 앱의 쿠키 인증 체계와 다릅니다. <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>에 대 한 쿠키 인증 스키마를 제공 하지 않으면 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키")를 사용 합니다.

인증 쿠키의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로 `true`로 설정 됩니다. 인증 쿠키는 사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에 허용 됩니다. 자세한 내용은 <xref:security/gdpr#essential-cookies>을 참조하세요.

`Startup.Configure`에서 `UseAuthentication` 및 `UseAuthorization`를 호출 하 여 `HttpContext.User` 속성을 설정 하 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다. `UseEndpoints`를 호출 하기 전에 `UseAuthentication` 및 `UseAuthorization` 메서드를 호출 합니다.

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.

`Startup.ConfigureServices` 메서드에서 인증에 대 한 서비스 구성에 `CookieAuthenticationOptions`을 설정 합니다.

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>쿠키 정책 미들웨어

쿠키 [정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) 는 쿠키 정책 기능을 사용 하도록 설정 합니다. 미들웨어를 앱 처리 파이프라인에 추가 하는 작업은 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 주는&mdash;순서를 구분 합니다.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

쿠키 정책 미들웨어에 제공 된 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>를 사용 하 여 쿠키를 추가 하거나 삭제할 때 쿠키 처리 및 쿠키 처리 처리기에 대 한 전역 특성을 제어 합니다.

기본 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 값은 OAuth2 인증을 허용 하는 `SameSiteMode.Lax`입니다. `SameSiteMode.Strict`의 동일한 사이트 정책을 엄격 하 게 적용 하려면 `MinimumSameSitePolicy`를 설정 합니다. 이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 크로스-원본 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 쿠키 보안 수준을 강화 합니다.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

`MinimumSameSitePolicy`에 대 한 쿠키 정책 미들웨어 설정은 아래 행렬에 따라 `CookieAuthenticationOptions` 설정의 `Cookie.SameSite` 설정에 영향을 줄 수 있습니다.

| MinimumSameSitePolicy | Cookie.SameSite | 결과 쿠키. SameSite 설정 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>인증 쿠키 만들기

사용자 정보를 포함 하는 쿠키를 만들려면 <xref:System.Security.Claims.ClaimsPrincipal>를 생성 합니다. 사용자 정보는 serialize 되어 쿠키에 저장 됩니다. 

필요한 <xref:System.Security.Claims.Claim>s를 사용 하 여 <xref:System.Security.Claims.ClaimsIdentity>를 만들고 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>를 호출 하 여 사용자를 로그인 합니다.

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`는 암호화 된 쿠키를 만들어 현재 응답에 추가 합니다. `AuthenticationScheme` 지정 하지 않으면 기본 체계가 사용 됩니다.

ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다. 여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 쿠키를 삭제 하려면 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>를 호출 합니다.

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme` (또는 "쿠키")가 구성표 (예: "ContosoCookie")로 사용 되지 않는 경우 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다. 그렇지 않으면 기본 체계가 사용 됩니다.

## <a name="react-to-back-end-changes"></a>백 엔드 변경 내용에 대응

쿠키가 만들어지면 쿠키는 id의 단일 소스입니다. 백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:

* 앱의 쿠키 인증 시스템이 인증 쿠키에 따라 요청을 계속 처리 합니다.
* 사용자는 인증 쿠키가 유효한 경우에만 앱에 로그인 상태를 유지 합니다.

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 이벤트를 사용 하 여 쿠키 id의 유효성 검사를 가로채 고 재정의할 수 있습니다. 모든 요청에서 쿠키의 유효성을 검사 하면 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.

쿠키 유효성 검사에 대 한 한 가지 방법은 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다. 사용자의 쿠키가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에는 해당 쿠키가 여전히 유효한 경우 사용자를 다시 인증할 필요가 없습니다. 샘플 앱에서 데이터베이스는 `IUserRepository`에서 구현 되 고 `LastChanged` 값을 저장 합니다. 데이터베이스에서 사용자를 업데이트 하는 경우 `LastChanged` 값은 현재 시간으로 설정 됩니다.

`LastChanged` 값에 따라 데이터베이스가 변경 될 때 쿠키를 무효화 하려면 데이터베이스의 현재 `LastChanged` 값을 포함 하는 `LastChanged` 클레임을 사용 하 여 쿠키를 만듭니다.

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

`ValidatePrincipal` 이벤트에 대 한 재정의를 구현 하려면 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다.

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

다음은 `CookieAuthenticationEvents`구현에 대 한 예입니다.

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

`Startup.ConfigureServices` 메서드에서 쿠키 서비스를 등록 하는 동안 events 인스턴스를 등록 합니다. `CustomCookieAuthenticationEvents` 클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다.

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

보안에 영향을 주지 않는 결정&mdash;사용자 이름이 업데이트 되는 상황을 고려 합니다. Destructively를 사용 하지 않는 사용자 계정을 업데이트 하려면 `context.ReplacePrincipal`를 호출 하 고 `context.ShouldRenew` 속성을 `true`로 설정 합니다.

> [!WARNING]
> 여기에 설명 된 방법은 모든 요청에서 트리거됩니다. 모든 요청에서 모든 사용자에 대 한 인증 쿠키의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.

## <a name="persistent-cookies"></a>영구 쿠키

쿠키를 브라우저 세션 간에 유지 하려고 할 수 있습니다. 이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다. 

다음 코드 조각에서는 브라우저 클로저를 통해 생존 하는 id 및 해당 쿠키를 만듭니다. 이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다. 브라우저가 닫히는 동안 쿠키가 만료 되 면 브라우저가 다시 시작 되 면 쿠키를 지웁니다.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true`로 설정 합니다.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>절대 쿠키 만료

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>를 사용 하 여 절대 만료 시간을 설정할 수 있습니다. 영구 쿠키를 만들려면 `IsPersistent`도 설정 해야 합니다. 그렇지 않으면 쿠키는 세션 기반 수명으로 만들어지고이 쿠키는 보유 하 고 있는 인증 티켓 전후에 만료 될 수 있습니다. `ExpiresUtc` 설정 된 경우 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>의 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 옵션 값 (설정 된 경우)을 재정의 합니다.

다음 코드 조각에서는 20 분 동안 지속 되는 id 및 해당 쿠키를 만듭니다. 이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core Id는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다. 그러나 ASP.NET Core Id가 없는 쿠키 기반 인증 인증 공급자를 사용할 수 있습니다. 자세한 내용은 <xref:security/authentication/identity>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다. **전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다. 사용자는 *Pages/Account/Login. cshtml* 파일의 `AuthenticateUser` 메서드에서 인증 됩니다. 실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.

## <a name="configuration"></a>Configuration

앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지에 대 한 패키지 참조를 만듭니다.

`Startup.ConfigureServices` 메서드에서 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 및 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 메서드를 사용 하 여 인증 미들웨어 서비스를 만듭니다.

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

`AddAuthentication`에 전달 된 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>는 앱에 대 한 기본 인증 체계를 설정 합니다. `AuthenticationScheme`은 쿠키 인증 인스턴스가 여러 개 있고 [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다. `AuthenticationScheme`를 [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)로 설정하면 구성표에 "쿠키" 값이 제공됩니다. 체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.

앱의 인증 체계가 앱의 쿠키 인증 체계와 다릅니다. <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>에 대 한 쿠키 인증 스키마를 제공 하지 않으면 `CookieAuthenticationDefaults.AuthenticationScheme` ("쿠키")를 사용 합니다.

인증 쿠키의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로 `true`로 설정 됩니다. 인증 쿠키는 사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에 허용 됩니다. 자세한 내용은 <xref:security/gdpr#essential-cookies>을 참조하세요.

`Startup.Configure` 메서드에서 `UseAuthentication` 메서드를 호출 하 여 `HttpContext.User` 속성을 설정 하는 인증 미들웨어를 호출 합니다. `UseMvcWithDefaultRoute` 또는 `UseMvc`를 호출 하기 전에 `UseAuthentication` 메서드를 호출 합니다.

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.

`Startup.ConfigureServices` 메서드에서 인증에 대 한 서비스 구성에 `CookieAuthenticationOptions`을 설정 합니다.

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>쿠키 정책 미들웨어

쿠키 [정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) 는 쿠키 정책 기능을 사용 하도록 설정 합니다. 미들웨어를 앱 처리 파이프라인에 추가 하는 작업은 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 주는&mdash;순서를 구분 합니다.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

쿠키 정책 미들웨어에 제공 된 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>를 사용 하 여 쿠키를 추가 하거나 삭제할 때 쿠키 처리 및 쿠키 처리 처리기에 대 한 전역 특성을 제어 합니다.

기본 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 값은 OAuth2 인증을 허용 하는 `SameSiteMode.Lax`입니다. `SameSiteMode.Strict`의 동일한 사이트 정책을 엄격 하 게 적용 하려면 `MinimumSameSitePolicy`를 설정 합니다. 이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 크로스-원본 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 쿠키 보안 수준을 강화 합니다.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

`MinimumSameSitePolicy`에 대 한 쿠키 정책 미들웨어 설정은 아래 행렬에 따라 `CookieAuthenticationOptions` 설정의 `Cookie.SameSite` 설정에 영향을 줄 수 있습니다.

| MinimumSameSitePolicy | Cookie.SameSite | 결과 쿠키. SameSite 설정 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>인증 쿠키 만들기

사용자 정보를 포함 하는 쿠키를 만들려면 <xref:System.Security.Claims.ClaimsPrincipal>를 생성 합니다. 사용자 정보는 serialize 되어 쿠키에 저장 됩니다. 

필요한 <xref:System.Security.Claims.Claim>s를 사용 하 여 <xref:System.Security.Claims.ClaimsIdentity>를 만들고 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>를 호출 하 여 사용자를 로그인 합니다.

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`는 암호화 된 쿠키를 만들어 현재 응답에 추가 합니다. `AuthenticationScheme` 지정 하지 않으면 기본 체계가 사용 됩니다.

ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다. 여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.

## <a name="sign-out"></a>로그아웃

현재 사용자를 로그 아웃 하 고 쿠키를 삭제 하려면 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>를 호출 합니다.

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme` (또는 "쿠키")가 구성표 (예: "ContosoCookie")로 사용 되지 않는 경우 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다. 그렇지 않으면 기본 체계가 사용 됩니다.

## <a name="react-to-back-end-changes"></a>백 엔드 변경 내용에 대응

쿠키가 만들어지면 쿠키는 id의 단일 소스입니다. 백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:

* 앱의 쿠키 인증 시스템이 인증 쿠키에 따라 요청을 계속 처리 합니다.
* 사용자는 인증 쿠키가 유효한 경우에만 앱에 로그인 상태를 유지 합니다.

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> 이벤트를 사용 하 여 쿠키 id의 유효성 검사를 가로채 고 재정의할 수 있습니다. 모든 요청에서 쿠키의 유효성을 검사 하면 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.

쿠키 유효성 검사에 대 한 한 가지 방법은 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다. 사용자의 쿠키가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에는 해당 쿠키가 여전히 유효한 경우 사용자를 다시 인증할 필요가 없습니다. 샘플 앱에서 데이터베이스는 `IUserRepository`에서 구현 되 고 `LastChanged` 값을 저장 합니다. 데이터베이스에서 사용자를 업데이트 하는 경우 `LastChanged` 값은 현재 시간으로 설정 됩니다.

`LastChanged` 값에 따라 데이터베이스가 변경 될 때 쿠키를 무효화 하려면 데이터베이스의 현재 `LastChanged` 값을 포함 하는 `LastChanged` 클레임을 사용 하 여 쿠키를 만듭니다.

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

`ValidatePrincipal` 이벤트에 대 한 재정의를 구현 하려면 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다.

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

다음은 `CookieAuthenticationEvents`구현에 대 한 예입니다.

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

`Startup.ConfigureServices` 메서드에서 쿠키 서비스를 등록 하는 동안 events 인스턴스를 등록 합니다. `CustomCookieAuthenticationEvents` 클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다.

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

보안에 영향을 주지 않는 결정&mdash;사용자 이름이 업데이트 되는 상황을 고려 합니다. Destructively를 사용 하지 않는 사용자 계정을 업데이트 하려면 `context.ReplacePrincipal`를 호출 하 고 `context.ShouldRenew` 속성을 `true`로 설정 합니다.

> [!WARNING]
> 여기에 설명 된 방법은 모든 요청에서 트리거됩니다. 모든 요청에서 모든 사용자에 대 한 인증 쿠키의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.

## <a name="persistent-cookies"></a>영구 쿠키

쿠키를 브라우저 세션 간에 유지 하려고 할 수 있습니다. 이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다. 

다음 코드 조각에서는 브라우저 클로저를 통해 생존 하는 id 및 해당 쿠키를 만듭니다. 이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다. 브라우저가 닫히는 동안 쿠키가 만료 되 면 브라우저가 다시 시작 되 면 쿠키를 지웁니다.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true`로 설정 합니다.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>절대 쿠키 만료

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>를 사용 하 여 절대 만료 시간을 설정할 수 있습니다. 영구 쿠키를 만들려면 `IsPersistent`도 설정 해야 합니다. 그렇지 않으면 쿠키는 세션 기반 수명으로 만들어지고이 쿠키는 보유 하 고 있는 인증 티켓 전후에 만료 될 수 있습니다. `ExpiresUtc` 설정 된 경우 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>의 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 옵션 값 (설정 된 경우)을 재정의 합니다.

다음 코드 조각에서는 20 분 동안 지속 되는 id 및 해당 쿠키를 만듭니다. 이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [정책 기반 역할 검사](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
