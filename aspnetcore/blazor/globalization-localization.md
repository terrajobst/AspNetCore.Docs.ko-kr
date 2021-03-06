---
title: ASP.NET Core Blazor 세계화 및 지역화
author: guardrex
description: 여러 문화권과 언어의 사용자가 Razor 구성 요소에 액세스할 수 있도록 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644895"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor 세계화 및 지역화

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

여러 문화권과 언어의 사용자가 Razor 구성 요소에 액세스할 수 있습니다. 다음 .NET 세계화 및 지역화 시나리오를 사용할 수 있습니다.

* . NET의 리소스 시스템
* 문화권별 숫자 및 날짜 형식 지정

다음과 같은 제한된 ASP.NET Core의 지역화 시나리오가 현재 지원됩니다.

* `IStringLocalizer<>`는  Blazor 앱에서 지원됩니다.
* `IHtmlLocalizer<>`, `IViewLocalizer<>` 및 데이터 주석 지역화는 ASP.NET Core MVC 시나리오로, Blazor 앱에서 **지원되지 않습니다**.

자세한 내용은 <xref:fundamentals/localization>를 참조하세요.

## <a name="globalization"></a>전역화

Blazor의 `@bind` 기능은 사용자의 현재 문화권에 따라 표시하기 위해 형식을 지정하고 값을 구문 분석합니다.

현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)는 다음 필드 형식(`<input type="{TYPE}" />`)에 사용됩니다.

* `date`
* `number`

이전 필드 형식:

* 적절한 브라우저 기반 서식 지정 규칙을 사용하여 표시됩니다.
* 자유 형식 텍스트를 포함할 수 없습니다.
* 브라우저의 구현에 따라 사용자 상호 작용 특성을 제공합니다.

다음 필드 형식은 특정 형식 지정 요구 사항이 있으며, 모든 주요 브라우저에서 지원되는 것은 아니므로 현재 Blazor에서 지원되지 않습니다.

* `datetime-local`
* `month`
* `week`

`@bind`는 `@bind:culture` 매개 변수를 지원하여 값을 구문 분석하고 서식을 지정하기 위한 <xref:System.Globalization.CultureInfo?displayProperty=fullName>을 제공합니다. `date` 및 `number` 필드 형식을 사용할 때는 문화권을 지정하지 않는 것이 좋습니다. `date` 및 `number`는 필수 문화권을 제공하는 Blazor가 기본적으로 제공되어 있습니다.

## <a name="localization"></a>지역화

Blazor 서버 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용하여 지역화됩니다. 미들웨어는 앱에서 리소스를 요청하는 사용자에게 적절한 문화권을 선택합니다.

문화권은 다음 방법 중 하나를 사용하여 설정할 수 있습니다.

* [쿠키](#cookies)
* [문화권을 선택하기 위한 UI 제공](#provide-ui-to-choose-the-culture)

자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>국제화를 위한 링커 구성(Blazor WebAssembly)

기본적으로 Blazor WebAssembly 앱에 대한 Blazor의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다. 링커 동작을 제어하는 방법에 대한 자세한 내용과 지침은 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>을 참조하세요.

### <a name="cookies"></a>쿠키

지역화 문화권 쿠키는 사용자의 문화권을 유지할 수 있습니다. 쿠키는 앱의 호스트 페이지(*Pages/Host.cshtml.cs*)의 `OnGet` 메서드에서 생성됩니다. 지역화 미들웨어는 후속 요청에서 쿠키를 읽어 사용자의 문화권을 설정합니다. 

쿠키를 사용하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있게 됩니다. 지역화 체계가 URL 경로 또는 쿼리 문자열을 기준으로 하는 경우 스키마는 WebSocket을 사용하지 못할 수 있으므로 문화권이 유지되지 않습니다. 따라서 지역화 문화권 쿠키를 사용하는 것이 좋습니다.

문화권이 지역화 쿠키에 유지되는 경우 임의 기술을 사용하여 문화권을 할당할 수 있습니다. 앱에 서버 쪽 ASP.NET Core에 대해 설정된 지역화 체계가 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용하고 앱의 체계 내에서 지역화 문화권 쿠키를 설정합니다.

다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 쿠키의 현재 문화권을 설정하는 방법을 보여 줍니다. Blazor 서버 앱에 다음 내용이 포함된 *Pages/Host.cshtml.cs* 파일을 만듭니다.

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

지역화는 다음과 같은 이벤트 시퀀스에 따라 앱에서 처리됩니다.

1. 브라우저가 앱에 초기 HTTP 요청을 보냅니다.
1. 문화권은 지역화 미들웨어에 의해 할당됩니다.
1. *_Host.cshtml.cs*의 `OnGet` 메서드는 응답의 일부로 쿠키에 문화권을 유지합니다.
1. 브라우저는 WebSocket 연결을 열어 대화형 Blazor 서버 세션을 만듭니다.
1. 지역화 미들웨어는 쿠키를 읽고 문화권을 할당합니다.
1. Blazor 서버 세션이 올바른 문화권으로 시작합니다.

### <a name="provide-ui-to-choose-the-culture"></a>문화권을 선택하기 위한 UI 제공

사용자가 문화권을 선택하기 위한 UI를 제공하려면 *리디렉션 기반 접근 방식*이 권장됩니다. 이 프로세스는 사용자가 보안 리소스에 액세스하려고 할 때 웹앱에서 발생하는 작업과 비슷합니다. 즉, 사용자는 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션됩니다. 

앱은 컨트롤러에 대한 리디렉션을 통해 사용자가 선택한 문화권을 유지합니다. 컨트롤러는 사용자가 선택한 문화권을 쿠키로 설정하고 사용자를 원래 URI로 다시 리디렉션합니다.

서버에서 HTTP 엔드포인트를 설정하여 사용자가 선택한 문화권을 쿠키에 설정하고 다시 원래 URI로 리디렉션합니다.

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> `LocalRedirect` 작업 결과를 사용하여 오픈 리디렉션 공격을 방지합니다. 자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.

다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행하는 방법의 예를 보여 줍니다.

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/localization>
