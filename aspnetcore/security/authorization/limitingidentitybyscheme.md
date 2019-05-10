---
title: ASP.NET Core에서 특정 구성표로 권한 부여
author: rick-anderson
description: 이 문서에는 여러 인증 방법을 사용 하는 경우 id는 특정 체계를 제한 하는 방법을 설명 합니다.
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897340"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>ASP.NET Core에서 특정 구성표로 권한 부여

이 단일 페이지 응용 프로그램 (Spa) 등의 일부 시나리오에서 여러 인증 방법을 일반적입니다. 예를 들어, 앱 JavaScript 요청에 대 한 JWT 전달자 인증 및 쿠키 기반 인증 로그인을 사용할 수 있습니다. 경우에 따라 앱에 인증 처리기를 여러 개 있을 수 있습니다. 예를 들어 있는 기본 id를 포함 하는 두 명의 쿠키 처리기와 multi-factor authentication (MFA)가 트리거되면 생성 됩니다. MFA는 사용자 추가 보안이 필요한 작업을 요청 하기 때문에 트리거될 수 있습니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

인증 체계를 인증 하는 동안 인증 서비스를 구성할 때 지정 됩니다. 예를 들어:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

위의 코드에서 두 명의 인증 처리기 추가 되었습니다: 쿠키 및 전달자에 대 한 하나입니다.

>[!NOTE]
>기본 스키마를 지정 하면를 `HttpContext.User` 해당 id로 설정 되는 속성입니다. 해당 동작은 필요 하지 않지만, 하는 경우 매개 변수가 없는 형태로 호출 하 여 해제 `AddAuthentication`합니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

인증 체계에는 인증 미들웨어가 인증 하는 동안 구성 된 경우 이름이 지정 됩니다. 예를 들어:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

위의 코드에서 인증 미들웨어 두 개 추가 되었습니다: 쿠키 및 전달자에 대 한 하나입니다.

>[!NOTE]
>기본 스키마를 지정 하면를 `HttpContext.User` 해당 id로 설정 되는 속성입니다. 해당 동작은 필요 하지 않지만, 하는 경우 사용 하지 않도록 설정 하 여 합니다 `AuthenticationOptions.AutomaticAuthenticate` 속성을 `false`입니다.

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Authorize 특성을 사용 하 여 스키마를 선택합니다.

권한 부여 시점에서 앱에 사용할 처리기를 나타냅니다. 처리기는 앱의 인증 체계를 쉼표로 구분 된 목록을 전달 하 여 권한이 부여 됩니다 선택 `[Authorize]`합니다. `[Authorize]` 특성이 인증 체계 또는 기본값 구성 되어 있는지 여부에 관계 없이 사용할 체계를 지정 합니다. 예를 들어:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

---

앞의 예제에서 쿠키와 전달자 처리기 실행 되며를 만들고 현재 사용자에 대 한 id를 추가할 수 있는 기회를 갖습니다. 만 단일 구성표를 지정 하 여 해당 처리기를 실행 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

위의 코드에서 "Bearer" 체계를 사용 하 여 처리기만 실행 됩니다. 쿠키 기반 id는 무시 됩니다.

## <a name="selecting-the-scheme-with-policies"></a>정책 사용 하 여 스키마를 선택합니다.

원하는 스키마를 지정 하려는 경우 [정책](xref:security/authorization/policies)를 설정할 수 있습니다는 `AuthenticationSchemes` 컬렉션 정책을 추가 하는 경우:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

앞의 예제에서 "Over18" 정책 "Bearer" 처리기에 의해 생성 된 id에 대해 에서만 실행 됩니다. 정책을 설정 하 여 사용 합니다 `[Authorize]` 특성의 `Policy` 속성:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>여러 인증 체계를 사용 합니다.

일부 앱은 여러 유형의 인증을 지원 해야 합니다. 예를 들어 앱 사용자 데이터베이스 뿐만 아니라 Azure Active Directory에서 사용자를 인증할 수 있습니다. 또 다른 예로 Active Directory Federation Services와 Azure Active Directory B2C에서 사용자를 인증 하는 앱입니다. 이 경우 앱에서 여러 발급자 JWT 전달자 토큰을 수락 해야 합니다.

적용 하려는 모든 인증 체계를 추가 합니다. 다음 코드 예를 들어 `Startup.ConfigureServices` 다른 발급자를 사용 하 여 두 JWT 전달자 인증 체계를 추가 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> 기본 인증 체계를 사용 하 여 JWT 전달자 인증을 하나만 등록 `JwtBearerDefaults.AuthenticationScheme`합니다. 추가 인증 고유 인증 체계를 사용 하 여 등록 해야 합니다.

다음 단계는 모두 인증 체계를 적용 하는 기본 권한 부여 정책을 업데이트 하는 것입니다. 예를 들어:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

기본 권한 부여 정책이 재정의 되는지 때 사용할 수는 `[Authorize]` 컨트롤러의 특성입니다. 컨트롤러는 다음 첫 번째 또는 두 번째 발급자가 발급 한 JWT를 사용 하 여 요청을 수락 합니다.

::: moniker-end
