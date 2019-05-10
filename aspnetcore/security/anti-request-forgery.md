---
title: ASP.NET Core에서 방지 교차 사이트 요청 위조 (XSRF/CSRF) 공격
author: steve-smith
description: 악성 웹 사이트는 클라이언트 브라우저와 앱 간의 상호 작용에 영향을 줄 수 있는 웹 앱에 대 한 공격을 방지 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 4498bd88e7188ea6c0063aeec054bc20e13ed5f1
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086437"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>ASP.NET Core에서 방지 교차 사이트 요청 위조 (XSRF/CSRF) 공격

하 여 [Steve Smith](https://ardalis.com/)하십시오 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

교차 사이트 요청 위조 (XSRF 또는 CSRF, 발음 라고도 *참조 surf*)은 악의적인 웹 앱을 신뢰 하는 웹 앱을 클라이언트 브라우저 간의 상호 작용에 영향을 줄 가능해 집니다 웹에 호스팅된 앱에 대 한 공격 브라우저. 웹 브라우저는 웹 사이트에 몇 가지 유형의 인증 토큰이 모든 요청에 자동으로 보내기 때문에 이러한 공격 있을 수 있습니다. 이러한 형태의 악용이 라고도 *원클릭 공격* 또는 *세션 있는* 공격을 활용 하기 때문에 사용자의 이전 세션을 인증 합니다.

CSRF 공격의 예:

1. 사용자가 로그인 `www.good-banking-site.com` 폼 인증을 사용 합니다. 사용자를 인증 하 고 인증 쿠키를 포함 하는 응답을 발급 하는 서버입니다. 사이트는 유효한 인증 쿠키를 사용 하 여 수신 하는 모든 요청을 신뢰 하기 때문에 공격에 취약 합니다.
1. 사용자가 악성 사이트를 방문 `www.bad-crook-site.com`합니다.

   악성 사이트 `www.bad-crook-site.com`, 다음과 같은 HTML 폼을 포함 합니다.

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   양식의 `action` 악성 사이트 아니라 한 취약 한 사이트에 게시 합니다. CSRF의 "교차 사이트" 부분입니다.

1. 사용자가 제출 단추를 선택 합니다. 브라우저 요청 및 요청 된 도메인에 대 한 인증 쿠키를 자동으로 포함 `www.good-banking-site.com`합니다.
1. 요청에서 실행 되는 `www.good-banking-site.com` 사용자의 인증 컨텍스트를 사용 하 여 서버 인증된 된 사용자가 수행할 수 있는 모든 작업을 수행할 수 있습니다.

사용자가 양식을 제출 단추를 선택 하는 있는 경우 외에도 악의적인 사이트는 다음을 수행할 수 있습니다.

* 자동으로 폼을 전송 하는 스크립트를 실행 합니다.
* 폼 제출을 AJAX 요청을 보냅니다.
* CSS를 사용 하 여 폼을 숨깁니다.

이러한 대체 시나리오에는 모든 작업 또는 악성 사이트를 처음 방문 이외의 사용자의 입력 필요 하지 않습니다.

HTTPS를 사용 하 여 CSRF 공격을 방지 하지 않습니다. 악성 사이트를 보낼 수는 `https://www.good-banking-site.com/` 안전 하지 않은 요청을 보낼 수 것 처럼 손쉽게 요청 합니다.

몇 가지 공격 대상 이미지 태그를 작업을 수행 하는 경우에 사용할 수 GET 요청에 응답 하는 끝점입니다. 이 형태의 공격이 이미지를 허용 하지만 JavaScript를 차단 하는 포럼 사이트에 일반적입니다. 변수 또는 리소스 변경는 GET 요청에서 상태를 변경 하는 앱은 악의적인 공격에 취약 합니다. **GET 요청 상태를 변경 하는 보호 되지 않습니다. GET 요청에서 상태를 변경 되지 않습니다 하는 것이 좋습니다.**

CSRF 공격 하기 때문에 인증 쿠키를 사용 하는 웹 앱에 대해 나타날 수 있습니다.

* 브라우저는 웹 앱에서 발급 한 쿠키를 저장 합니다.
* 저장 된 쿠키는 인증 된 사용자에 대 한 세션 쿠키를 포함합니다.
* 브라우저의 쿠키를 연결 된 모든 웹 앱에 도메인을 사용 하 여 브라우저 내에서 앱에 요청을 생성할 하는 방법에 관계 없이 모든 요청을 보냅니다.

하지만 CSRF 공격만 제한 되지 않습니다 쿠키를 이용 합니다. 예를 들어, 기본 및 다이제스트 인증은 또한 취약 합니다. 브라우저 세션까지 자격 증명을 자동으로 보냅니다 사용자가 기본 또는 다이제스트 인증으로 로그인 하는 것을 후&dagger; 종료 합니다.

&dagger;이 컨텍스트에서 *세션* 사용자가 인증 되는 클라이언트 쪽 세션을 가리킵니다. 서버 쪽 세션에 관련 되지 않은 또는 [ASP.NET Core 세션 미들웨어](xref:fundamentals/app-state)합니다.

사용자는 예방 조치를 수행 하 여 CSRF 취약성 으로부터 보호할 수 있습니다.

* 사용을 완료 하는 경우 웹 앱으로 로그인 합니다.
* 주기적으로 명확한 브라우저 쿠키입니다.

그러나 CSRF 취약점은 근본적으로 웹 앱을 최종 사용자가 아니라 문제입니다.

## <a name="authentication-fundamentals"></a>인증 기본 사항

쿠키 기반 인증은 인증의 인기 있는 폼입니다. 토큰 기반 인증 시스템은 단일 페이지 응용 프로그램 (Spa)에 대 한 특히 널리 사용 되 고, 증가 합니다.

### <a name="cookie-based-authentication"></a>쿠키 기반 인증

사용자가 자신의 사용자 이름과 암호를 사용 하 여 인증, 인증 및 권한 부여에 사용할 수 있는 인증 티켓을 포함 하는 토큰을 발급 하는 합니다. 모든 요청 클라이언트와 함께 제공 되는 쿠키를 사용 하면 토큰 저장. 생성 및이 쿠키 유효성 검사 쿠키 인증 미들웨어에서 수행 됩니다. 합니다 [미들웨어](xref:fundamentals/middleware/index) 암호화 된 쿠키에 사용자 보안 주체를 serialize 합니다. 후속 요청 시 미들웨어는 쿠키의 유효성을 검사, 주 서버를 다시 만듭니다 및 보안 주체를 할당 합니다 [사용자](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) 속성을 [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)합니다.

### <a name="token-based-authentication"></a>토큰 기반 인증

사용자가 인증 되 면 (없습니다 위조 방지 토큰) 토큰을 발급 하는 합니다. 토큰의 형태로 사용자 정보를 포함 [클레임](/dotnet/framework/security/claims-based-identity-model) 또는 앱에서 유지 관리 하는 사용자 상태를 앱을 가리키는 참조 토큰입니다. 사용자가 인증을 요구 하는 리소스에 액세스 하려고 하는 경우 토큰이 전달자 토큰의 형태로 추가 권한 부여 헤더를 사용 하 여 앱에 전송 됩니다. 이렇게 하면 앱 상태 비저장입니다. 각 후속 요청에서 서버 쪽 유효성 검사에 대 한 토큰 요청에 전달 됩니다. 이 토큰 되지 *암호화*; 있기 *인코딩된*합니다. 서버에서 토큰은 해당 정보에 액세스 하려면 디코딩됩니다. 후속 요청에 토큰을 보낼 토큰은 브라우저의 로컬 저장소에 저장 합니다. 토큰은 브라우저의 로컬 저장소에 저장 된 경우 CSRF 취약성에 대해 염려 하지. CSRF 중요 한 경우 토큰이 쿠키에 저장 됩니다.

### <a name="multiple-apps-hosted-at-one-domain"></a>하나의 도메인에서 호스팅되는 여러 앱

공유 호스팅 환경은 세션 하이재킹, CSRF, 로그인 및 다른 공격에 취약 합니다.

하지만 `example1.contoso.net` 및 `example2.contoso.net` 호스트인 다른 호스트 간에 암시적 신뢰 관계가 없는 `*.contoso.net` 도메인입니다. 이 암시적 트러스트 관계에는 신뢰할 수 없는 호스트를 (AJAX 요청을 제어 하는 동일 원본 정책을 반드시 HTTP 쿠키에 적용 되지 않습니다) 다른 사용자의 쿠키에 적용할 수 있습니다.

도메인을 공유 하지 않으므로 동일한 도메인에 호스트 된 앱 간에 신뢰할 수 있는 쿠키를 악용 하는 공격을 방지할 수 있습니다. 각 앱 자체 도메인에서 호스트 되는 경우 암시적 쿠키 트러스트 관계가 없는 악용할 수 있습니다.

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core 위조 방지 구성

> [!WARNING]
> 위조 방지를 사용 하 여 ASP.NET Core 구현 [ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)합니다. 데이터 보호 스택이 서버 팜에서 작동 하도록 구성 되어야 합니다. 참조 [데이터 보호 구성](xref:security/data-protection/configuration/overview) 자세한 내용은 합니다.

ASP.NET Core 2.0 이상에서는 합니다 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) HTML 폼 요소에 위조 방지 토큰을 삽입 합니다. Razor 파일의 다음 태그는 위조 방지 토큰을 자동으로 생성 합니다.

```cshtml
<form method="post">
    ...
</form>
```

마찬가지로 [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 양식의 메서드가 GET 되지 않으면 기본적으로 위조 방지 토큰을 생성 합니다.

발생 하는 HTML 폼 요소 위조 방지 토큰 자동으로 생성 때 합니다 `<form>` 태그를 포함 합니다 `method="post"` 특성 및 다음 중 하나에 해당할:

* Action 특성은 빈 (`action=""`).
* Action 특성을 제공 하지 않으면 (`<form method="post">`).

HTML 폼 요소에 대 한 위조 방지 토큰 자동 생성을 비활성화할 수 있습니다.

* 명시적으로 사용 하 여 위조 방지 토큰을 사용 하지 않도록 설정 된 `asp-antiforgery` 특성:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Form 요소는 선택한 스케일 아웃 태그 도우미의 태그 도우미를 사용 하 여 [! 옵트아웃 기호](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* 제거 된 `FormTagHelper` 뷰에서 합니다. `FormTagHelper` Razor 보기에는 다음 지시문을 추가 하 여 보기에서 제거할 수 있습니다.

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor 페이지](xref:razor-pages/index) XSRF/CSRF에서 자동으로 보호 됩니다. 자세한 내용은 [XSRF/CSRF 및 Razor 페이지](xref:razor-pages/index#xsrf)합니다.

가장 일반적인 방법은 CSRF 공격 으로부터 보호 하는 데 사용 하는 것은 *동기화 장치 토큰 패턴이* (STP). STP은 사용자 양식 데이터를 사용 하 여 페이지를 요청할 때 사용 됩니다.

1. 서버는 클라이언트에 현재 사용자의 id와 연결 된 토큰을 보냅니다.
1. 확인을 위해 서버에 클라이언트 전송 토큰 다시 합니다.
1. 서버에서 인증 된 사용자 id와 일치 하지 않는 토큰을 받으면 요청 거부 됩니다.

토큰을 고유 하 고 예측할 수 없는 경우 일련의 요청을 적절 한 시퀀싱 되도록 토큰을 사용할 수도 있습니다 (예를 들어 요청 시퀀스를 확인 합니다. 1 페이지 &ndash; 2 페이지 &ndash; 3 페이지). 모든 ASP.NET Core MVC 및 Razor 페이지 템플릿에서 양식의 위조 방지 토큰을 생성 합니다. 예제 보기의 다음 쌍 위조 방지 토큰을 생성 합니다.

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

명시적으로 추가 하는 위조 방지 토큰을 `<form>` 요소는 HTML 도우미를 사용 하 여 태그 도우미를 사용 하지 않고 [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

위의 경우 중 각각의 ASP.NET Core에 다음과 유사한 숨겨진된 양식 필드를 추가합니다.

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core를 포함 세 [필터](xref:mvc/controllers/filters) 위조 방지 토큰을 사용 합니다.

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>위조 방지 옵션

사용자 지정 [위조 방지 옵션](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) 에서 `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;antiforgery 설정 `Cookie` 의 속성을 사용 하 여 속성을 [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) 클래스입니다.

| 옵션 | 설명 |
| ------ | ----------- |
| [쿠키](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 위조 방지 쿠키를 만드는 데 설정을 결정 합니다. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 위조 방지 토큰 보기에서 렌더링할 위조 방지 시스템에서 사용 하는 숨겨진된 폼 필드의 이름입니다. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 위조 방지 시스템에서 사용 하는 헤더의 이름입니다. 경우 `null`, 시스템 데이터 형식에만 고려 합니다. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 생성을 보류할 지 여부를 지정 된 `X-Frame-Options` 헤더입니다. 기본적으로 헤더는 "SAMEORIGIN"의 값을 사용 하 여 생성 됩니다. 기본값은 `false`입니다. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| 옵션 | 설명 |
| ------ | ----------- |
| [쿠키](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 위조 방지 쿠키를 만드는 데 설정을 결정 합니다. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | 쿠키의 도메인입니다. 기본값은 `null`입니다. 이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다. 권장 되는 대안은 Cookie.Domain을 보여 줍니다. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | 쿠키의 이름입니다. 설정 되지 않은 시스템 생성 고유 이름이 합니다 [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery입니다. ")입니다. 이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다. 권장 되는 대안은 Cookie.Name을 보여 줍니다. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | 쿠키에 설정 된 경로입니다. 이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다. 권장 되는 대안은 Cookie.Path을 보여 줍니다. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 위조 방지 토큰 보기에서 렌더링할 위조 방지 시스템에서 사용 하는 숨겨진된 폼 필드의 이름입니다. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 위조 방지 시스템에서 사용 하는 헤더의 이름입니다. 경우 `null`, 시스템 데이터 형식에만 고려 합니다. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 위조 방지 시스템에서 HTTPS가 필요한 지 여부를 지정 합니다. 경우 `true`, HTTPS가 아닌 요청이 실패 합니다. 기본값은 `false`입니다. 이 속성은 사용 되지 않습니다 및 이후 버전에서 제거 됩니다. 권장된 대안 Cookie.SecurePolicy를 설정 하는 것입니다. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 생성을 보류할 지 여부를 지정 된 `X-Frame-Options` 헤더입니다. 기본적으로 헤더는 "SAMEORIGIN"의 값을 사용 하 여 생성 됩니다. 기본값은 `false`입니다. |

::: moniker-end

자세한 내용은 [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)합니다.

## <a name="configure-antiforgery-features-with-iantiforgery"></a>IAntiforgery 된 위조 방지 기능을 구성 합니다.

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 위조 방지 기능을 구성 하는 API를 제공 합니다. `IAntiforgery` 요청할 수 있습니다 합니다 `Configure` 메서드는 `Startup` 클래스입니다. 다음 예제에서는 위조 방지 토큰을 생성 하 고 (기본 Angular 명명 규칙이이 항목의 뒷부분에 설명 된 사용)를 쿠키로이 응답에 보내는 앱의 홈 페이지에서 미들웨어를 사용 합니다.

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>위조 방지 유효성 검사 필요

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 은 개별 작업, 컨트롤러에 적용할 수 있는 작업 필터 또는 전역적으로 합니다. 요청에 유효한 위조 방지 토큰을 포함 하지 않는 한이 필터가 적용 하는 작업에 대 한 요청이 차단 됩니다.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

`ValidateAntiForgeryToken` 특성에 대 한 요청 작업 메서드에 HTTP GET 요청을 포함 하 여 데코레이팅되 토큰이 필요 합니다. 경우는 `ValidateAntiForgeryToken` 앱의 컨트롤러에서 특성 적용, 사용 하 여 재정의할 수 있습니다는 `IgnoreAntiforgeryToken` 특성입니다.

> [!NOTE]
> ASP.NET Core는 GET 요청을 위조 방지 토큰을 자동으로 추가 지원 하지 않습니다.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>자동으로 안전 하지 않은 HTTP 메서드에 대 한 위조 방지 토큰 유효성 검사

ASP.NET Core 앱 안전한 HTTP 메서드 (GET, HEAD, 옵션 및 추적)에 대 한 위조 방지 토큰을 생성 하지 않습니다. 광범위 하 게 적용 하는 대신를 `ValidateAntiForgeryToken` 특성을 사용 하 여 재정의 한 다음 `IgnoreAntiforgeryToken` 특성을 [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 특성을 사용할 수 있습니다. 이 특성은 동일 하 게 작동 합니다 `ValidateAntiForgeryToken` 특성을 제외 하 고 다음 HTTP 메서드를 사용 하 여 요청에 대 한 토큰 필요가:

* 가져오기
* HEAD
* 옵션
* TRACE

사용 좋습니다 `AutoValidateAntiforgeryToken` 아닌 API 시나리오에 대 한 광범위 하 게 합니다. 이렇게 하면 POST 작업은 기본적으로 보호 됩니다. 기본적으로 위조 방지 토큰을 무시 하지 않는 한 `ValidateAntiForgeryToken` 개별 작업 메서드에 적용 됩니다. 이 가능성이을 POST 작업 메서드에 대 한이 시나리오에서는 보호 되지 않은 실수로 CSRF 공격에 취약 한 해당 앱을 종료 합니다. 모든 게시물 위조 방지 토큰을 전송 해야 합니다.

Api 토큰의 쿠키 부분을 보내기 위한 자동 메커니즘이 필요가 없습니다. 아마도 구현 클라이언트 코드 구현에 따라 달라 집니다. 몇 가지 예는 다음과 같습니다.

클래스 수준 예제:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

전역 예제:

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a>전역 재정의 또는 컨트롤러 위조 방지 특성

합니다 [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) 필터는 위조 방지 토큰을 지정 된 작업 (또는 컨트롤러)에 대 한 필요성을 제거 하는 데 사용 됩니다. 이 필터 재정의 적용 하면 `ValidateAntiForgeryToken` 고 `AutoValidateAntiforgeryToken` (전역적으로 또는 컨트롤러)는 더 높은 수준에서 지정 된 필터입니다.

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>인증 후 새로 고침 토큰

뷰 또는 Razor 페이지에 사용자를 리디렉션하여 사용자가 인증 한 후 토큰 새로 고쳐야 합니다.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX 및 Spa

전통적인 HTML 기반 앱에서 위조 방지 토큰은 숨겨진된 양식 필드를 사용 하 여 서버에 전달 됩니다. 최신 JavaScript 기반 응용 프로그램 및 Spa에서 많은 요청이 프로그래밍 방식으로 이루어집니다. 이러한 AJAX 요청 토큰을 보낼 다른 기술 (예: 요청 헤더 또는 쿠키)를 사용할 수 있습니다.

쿠키 인증 토큰을 저장 하 고 서버의 API 요청을 인증 하기를 사용 하는 경우 CSRF 잠재적인 문제가 됩니다. 로컬 저장소를 사용 하 여 토큰을 저장, 하므로 로컬 저장소에서 값은 모든 요청을 사용 하 여 서버에 자동으로 전송 되지 않습니다 CSRF 취약점으로 인 한 완화 될 수 있습니다. 따라서 로컬 저장소를 사용 하 여 위조 방지 토큰을 요청 헤더는 권장된 방법으로 토큰을 보내는 클라이언트에 저장 합니다.

### <a name="javascript"></a>JavaScript

JavaScript를 사용 하 여 뷰를 사용 하 여, 토큰을 만들 수 있습니다 뷰 내에서 서비스를 사용 하 여. 삽입 된 [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 보기 및 호출 서비스 [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

이 방법은 서버에서 쿠키를 설정 하거나 클라이언트에서 읽기를 직접 처리 필요가 없습니다.

앞의 예제 JavaScript를 사용 하 여 AJAX POST 헤더에 대 한 숨겨진된 필드 값을 읽을 수 있습니다.

또한 JavaScript 쿠키에서 토큰에 액세스 하 고 쿠키의 콘텐츠를 사용 하 여 토큰의 값을 사용 하 여 헤더를 생성 수 있습니다.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

라고 하는 헤더에서 토큰을 보내도록 요청 스크립트 가정 `X-CSRF-TOKEN`를 검색할 위조 방지 서비스를 구성 합니다 `X-CSRF-TOKEN` 헤더:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

다음 예제에서는 JavaScript를 사용 하 여 적절 한 헤더를 사용 하 여 AJAX 요청을 확인 하십시오.

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS는 CSRF 주소로 규칙을 사용 합니다. 서버 이름의 쿠키를 보내면 `XSRF-TOKEN`, AngularJS `$http` 서비스 요청을 서버로 보낼 때 헤더에 쿠키 값을 추가 합니다. 이 프로세스는 자동입니다. 헤더를 클라이언트에서 명시적으로 설정할 필요가 없습니다. 헤더 이름은 `X-XSRF-TOKEN`합니다. 서버는이 헤더를 검색 하 고 해당 내용의 유효성을 검사 해야 합니다.

응용 프로그램 시작에이 규칙을 사용 하 여 작업을 ASP.NET Core api:

* 앱을 구성 하 라는 쿠키에서 토큰을 제공할 `XSRF-TOKEN`합니다.
* 라는 헤더에 대 한 검색할 위조 방지 서비스 구성 `X-XSRF-TOKEN`합니다.

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Antiforgery 확장

합니다 [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 형식 개발자가 각 토큰의 추가 데이터를 왕복 하 여 CSRF 방지 시스템의 동작을 확장할 수 있습니다. [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 때마다 메서드는 필드 토큰 생성 되 고 반환 값은 생성 된 토큰에 포함 되어 있습니다. 구현 자가 없습니다 타임 스탬프, nonce, 또는 다른 값을 반환 하 고 호출 하 [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 토큰 유효성을 검사할 때이 데이터를 유효성 검사를 합니다. 클라이언트의 사용자 이름 이미 생성 된 토큰에 포함 되어 있으므로이 정보를 포함 하지 않아도 됩니다. 했지만 추가 데이터 토큰에 포함 된 경우 `IAntiForgeryAdditionalDataProvider` 는 구성 추가 데이터 확인 되지 않습니다.

## <a name="additional-resources"></a>추가 자료

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 대 [Web Application Security Project를 열고](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
