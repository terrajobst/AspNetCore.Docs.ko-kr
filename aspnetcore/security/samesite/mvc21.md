---
title: ASP.NET Core 2.1 MVC SameSite cookie 샘플
author: rick-anderson
description: ASP.NET Core 2.1 MVC SameSite cookie 샘플
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite/mvc21
ms.openlocfilehash: 14f3d3d27d5740519e1ba57529d5694b4cdeb9ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654993"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>ASP.NET Core 2.1 MVC SameSite cookie 샘플

ASP.NET Core 2.1는 [SameSite](https://www.owasp.org/index.php/SameSite) 특성에 대 한 기본 제공 지원을 제공 하지만 원래 표준에 기록 되었습니다. [패치 된 동작이](https://github.com/dotnet/aspnetcore/issues/8212) `SameSite.None`의 의미를 변경 하 여 값을 전혀 내보내지 않고 `None`값으로 sameSite 특성을 내보냅니다. 값을 내보내지 않으려면 쿠키의 `SameSite` 속성을-1로 설정 하면 됩니다.

## <a name="sampleCode"></a>SameSite 특성 작성

다음은 쿠키에 SameSite 특성을 작성 하는 방법의 예입니다.

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>쿠키 인증 및 세션 상태 쿠키 설정

쿠키 인증, 세션 상태 및 [기타 다양 한 구성 요소](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 는 쿠키 옵션을 통해 sameSite 옵션을 설정 합니다. 예를 들면

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

위의 코드에서 쿠키 인증 및 세션 상태는 모두 sameSite 특성을 `None`설정 하 고 `None` 값을 사용 하 여 특성을 내보내고 보안 특성을 true로 설정 합니다.

### <a name="run-the-sample"></a>샘플 실행

[샘플 프로젝트](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)를 실행 하는 경우에는 초기 페이지에서 브라우저 디버거를 로드 하 고 사이트에 대 한 쿠키 컬렉션을 보는 데 사용 합니다. Edge 및 Chrome에서이 작업을 수행 하려면 `F12`를 클릭 한 다음 `Application` 탭을 선택 하 고 `Storage` 섹션의 `Cookies` 옵션 아래에서 사이트 URL을 클릭 합니다.

![브라우저 디버거 쿠키 목록](BrowserDebugger.png)

"SameSite 쿠키 만들기" 단추를 클릭 하면 샘플 [코드](#sampleCode)에 설정 된 값과 일치 하는 `Lax`SameSite 특성 값이 있는 위의 이미지에서 볼 수 있습니다.

## <a name="interception"></a>쿠키 가로채기

쿠키를 가로채 고 사용자의 브라우저 에이전트에서 지원에 따라 없음 값을 조정 하려면 `CookiePolicy` 미들웨어를 사용 해야 합니다. 이는 쿠키를 작성 하 고 `ConfigureServices()`내에서 구성 된 구성 요소 **보다 먼저** http 요청 파이프라인에 배치 해야 합니다.

파이프라인에 삽입 하려면 [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)의 `Configure(IApplicationBuilder, IHostingEnvironment)` 메서드에서 `app.UseCookiePolicy()`를 사용 합니다. 다음은 그 예입니다.

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

그런 다음 `ConfigureServices(IServiceCollection services)` 쿠키가 추가 되거나 삭제 될 때 도우미 클래스를 호출 하도록 쿠키 정책을 구성 합니다. 다음은 그 예입니다.

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

도우미 함수 `CheckSameSite(HttpContext, CookieOptions)`:

* 쿠키가 요청에 추가 되거나 요청에서 삭제 될 때 호출 됩니다.
* `SameSite` 속성이 `None`으로 설정 되어 있는지 확인 합니다.
* `SameSite`이 `None`으로 설정 되어 있고 현재 사용자 에이전트가 none 특성 값을 지원 하지 않는 것으로 알려진 경우 확인은 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) 클래스를 사용 하 여 수행 됩니다.
  * 속성을로 설정 하 여 값을 내보내지 않도록 `SameSite`을 설정 `(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>대상 지정 .NET Framework

ASP.NET Core 및 System.web (ASP.NET 클래식)에는 SameSite의 독립적인 구현이 있습니다. .NET Framework에 대 한 SameSite KB 패치는 ASP.NET Core를 사용 하는 경우에는 필요 하지 않으며 SameSite에 대 한 최소 프레임 워크 버전 요구 사항 (.NET 4.7.2)이 ASP.NET Core에 적용 됩니다.

.NET의 ASP.NET Core 적절 한 수정을 얻으려면 nuget 패키지 종속성을 업데이트 해야 합니다.

.NET Framework에 대 한 ASP.NET Core 변경 내용을 얻으려면 패치 된 패키지 및 버전 (2.1.14 이상 2.1 버전)에 대 한 직접 참조를 확인 합니다.

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>추가 정보
 
[Chrome 업데이트](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite 설명서](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite 변경 알림](https://github.com/dotnet/aspnetcore/issues/8212)