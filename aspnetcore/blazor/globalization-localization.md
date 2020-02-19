---
title: 세계화 및 지역화 ASP.NET Core Blazor
author: guardrex
description: 여러 문화권과 언어로 사용자가 Razor 구성 요소에 액세스할 수 있도록 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453189"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="e84b9-103">세계화 및 지역화 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e84b9-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="e84b9-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e84b9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e84b9-105">여러 문화권과 언어로 된 사용자가 Razor 구성 요소에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="e84b9-106">다음 .NET 전역화 및 지역화 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="e84b9-107">. NET의 리소스 시스템</span><span class="sxs-lookup"><span data-stu-id="e84b9-107">.NET's resources system</span></span>
* <span data-ttu-id="e84b9-108">문화권별 숫자 및 날짜 형식 지정</span><span class="sxs-lookup"><span data-stu-id="e84b9-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="e84b9-109">제한 된 ASP.NET Core의 지역화 시나리오 집합이 현재 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="e84b9-110">`IStringLocalizer<>`은 Blazor 앱에서 *지원 됩니다* .</span><span class="sxs-lookup"><span data-stu-id="e84b9-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="e84b9-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`및 데이터 주석 지역화는 MVC 시나리오 ASP.NET Core Blazor 앱에서 **지원 되지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="e84b9-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="e84b9-112">자세한 내용은 <xref:fundamentals/localization>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e84b9-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="e84b9-113">전역화</span><span class="sxs-lookup"><span data-stu-id="e84b9-113">Globalization</span></span>

Blazor<span data-ttu-id="e84b9-114">의 `@bind` 기능은 사용자의 현재 문화권에 따라 표시 하기 위해 형식을 수행 하 고 값을 구문 분석 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="e84b9-115">현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="e84b9-116">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) 는 다음 필드 형식 (`<input type="{TYPE}" />`)에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="e84b9-117">이전 필드 형식:</span><span class="sxs-lookup"><span data-stu-id="e84b9-117">The preceding field types:</span></span>

* <span data-ttu-id="e84b9-118">적절 한 브라우저 기반 서식 지정 규칙을 사용 하 여 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="e84b9-119">자유 형식 텍스트를 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="e84b9-120">브라우저의 구현에 따라 사용자 상호 작용 특성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="e84b9-121">다음 필드 형식은 특정 형식 지정 요구 사항을 가지 며, 모든 주요 브라우저에서 지원 되지 않기 때문에 현재 Blazor에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="e84b9-122">`@bind`는 `@bind:culture` 매개 변수를 지원 하 여 값을 구문 분석 하 고 서식을 지정 하는 <xref:System.Globalization.CultureInfo?displayProperty=fullName>을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="e84b9-123">`date` 및 `number` 필드 형식을 사용할 때는 문화권을 지정 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="e84b9-124">`date` 및 `number`에는 필수 문화권을 제공 하는 Blazor 지원이 기본적으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="e84b9-125">지역화</span><span class="sxs-lookup"><span data-stu-id="e84b9-125">Localization</span></span>

Blazor<span data-ttu-id="e84b9-126"> Server 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용 하 여 지역화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-126"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="e84b9-127">미들웨어는 앱에서 리소스를 요청 하는 사용자에 게 적절 한 문화권을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-127">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="e84b9-128">문화권은 다음 방법 중 하나를 사용 하 여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-128">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="e84b9-129">쿠키</span><span class="sxs-lookup"><span data-stu-id="e84b9-129">Cookies</span></span>](#cookies)
* [<span data-ttu-id="e84b9-130">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="e84b9-130">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="e84b9-131">자세한 내용과 예제는 <xref:fundamentals/localization>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e84b9-131">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="e84b9-132">국제화를 위한 링커 구성 (Blazor Weasembmbambmbemboma)</span><span class="sxs-lookup"><span data-stu-id="e84b9-132">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="e84b9-133">기본적으로 Blazor WebAssembly 앱에 대한 Blazor의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="e84b9-134">링커 동작을 제어 하는 방법에 대 한 자세한 내용과 지침은 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e84b9-134">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="e84b9-135">쿠키</span><span class="sxs-lookup"><span data-stu-id="e84b9-135">Cookies</span></span>

<span data-ttu-id="e84b9-136">지역화 문화권 쿠키는 사용자의 문화권을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-136">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="e84b9-137">쿠키는 앱의 호스트 페이지 (*Pages/host-a*)의 `OnGet` 메서드에서 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-137">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="e84b9-138">지역화 미들웨어는 후속 요청에서 쿠키를 읽어 사용자의 culture를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-138">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="e84b9-139">쿠키를 사용 하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-139">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="e84b9-140">지역화 체계가 URL 경로 또는 쿼리 문자열을 기반으로 하는 경우 스키마는 Websocket을 사용 하지 못할 수 있으므로 문화권이 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-140">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="e84b9-141">따라서 지역화 문화권 쿠키를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-141">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="e84b9-142">문화권이 지역화 쿠키에 유지 되는 경우 모든 기술을 사용 하 여 문화권을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-142">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="e84b9-143">서버 쪽 ASP.NET Core에 대해 설정 된 지역화 체계가 앱에 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용 하 고 앱의 체계 내에서 지역화 문화권 쿠키를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-143">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="e84b9-144">다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 쿠키의 현재 문화권을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-144">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="e84b9-145">Blazor Server 앱에서 다음 내용을 사용 하 여 *Pages/Host. cshtml* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-145">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="e84b9-146">지역화는 다음과 같은 이벤트 시퀀스에서 앱에 의해 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-146">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="e84b9-147">브라우저가 앱에 초기 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-147">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="e84b9-148">문화권이 지역화 미들웨어에 의해 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-148">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="e84b9-149">*_Host* 의 `OnGet` 메서드는 응답의 일부로 쿠키의 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-149">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="e84b9-150">브라우저는 WebSocket 연결을 열어 대화형 Blazor 서버 세션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-150">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="e84b9-151">지역화 미들웨어는 쿠키를 읽고 문화권을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-151">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="e84b9-152">Blazor 서버 세션이 올바른 문화권으로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-152">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="e84b9-153">문화권을 선택 하는 UI 제공</span><span class="sxs-lookup"><span data-stu-id="e84b9-153">Provide UI to choose the culture</span></span>

<span data-ttu-id="e84b9-154">사용자가 문화권을 선택할 수 있도록 UI를 제공 하려면 *리디렉션 기반 방법을* 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-154">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="e84b9-155">이 프로세스는 사용자가 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션되는&mdash;사용자가 보안 리소스에 액세스 하려고 할 때 웹 앱에서 발생 하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-155">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="e84b9-156">앱은 컨트롤러에 대 한 리디렉션을 통해 사용자가 선택한 문화권을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-156">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="e84b9-157">컨트롤러는 사용자가 선택한 문화권을 쿠키로 설정 하 고 사용자를 다시 원래 URI로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-157">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="e84b9-158">서버에서 HTTP 끝점을 설정 하 여 사용자가 선택한 문화권을 쿠키에 설정 하 고 다시 원래 URI로 리디렉션을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-158">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="e84b9-159">`LocalRedirect` 작업 결과를 사용 하 여 열린 리디렉션 공격을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-159">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="e84b9-160">자세한 내용은 <xref:security/preventing-open-redirects>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e84b9-160">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="e84b9-161">다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행 하는 방법의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e84b9-161">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e84b9-162">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e84b9-162">Additional resources</span></span>

* <xref:fundamentals/localization>
