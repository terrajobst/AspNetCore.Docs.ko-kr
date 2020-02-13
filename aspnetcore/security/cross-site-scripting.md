---
title: ASP.NET Core에서 교차 사이트 스크립팅(XSS) 방지하기
author: rick-anderson
description: 사이트 간 스크립팅 (XSS) 및 ASP.NET Core 앱에서이 취약성을 해결 하는 방법에 알아봅니다.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1d6f605dc336d8768b8a47e4995f119d198a61af
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172641"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="6a026-103">ASP.NET Core에서 교차 사이트 스크립팅(XSS) 방지하기</span><span class="sxs-lookup"><span data-stu-id="6a026-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="6a026-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6a026-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6a026-105">교차 사이트 스크립팅(XSS)은 공격자가 웹페이지에 Javascript와 같은 클라이언트측 스크립트를 웹페이지에 배치하도록 하는 보안상 취약점을 일컫습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="6a026-106">다른 사용자가 공격당한 페이지를 로드할 경우 쿠키 및 세션 토큰을 탈취하고 DOM을 조작하여 웹 페이지 내용을 변경하거나 다른 페이지로 이동시킬 수 있는 공격자의 스크립트가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="6a026-107">XSS 취약성으로 인 한 응용 프로그램은 사용자 입력 하 고 유효성 검사, 인코딩 또는 해 서 이스케이프 하지 않고 페이지를 출력 하는 경우에 일반적으로 발생 합니다. XSS 취약점은 응용 프로그램에서 사용자 입력을 받아 유효성 검사, 인코딩, 이스케이프 과정을 거치지 않고 페이지에 출력할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="6a026-108">응용 프로그램을 XSS로부터 보호하기</span><span class="sxs-lookup"><span data-stu-id="6a026-108">Protecting your application against XSS</span></span>

<span data-ttu-id="6a026-109">기본 수준의 XSS는 응용 프로그램을 속여에 삽입 하 여 렌더링 된 페이지에 `<script>` 태그를 삽입 하거나 요소에 `On*` 이벤트를 삽입 하는 방식으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="6a026-110">개발자는 이러한 XSS 공격으로부터 응용 프로그램을 보호하기 위해 다음과 같은 조치를 취해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="6a026-111">아래의 단계를 거치지 않는 한, 신뢰할 수 없는 데이터를 HTML 입력에 추가하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="6a026-112">신뢰할 수 없는 데이터란 공격자가 제어할 수 있는 모든 데이터를 의미하며, HTML 폼 입력, 쿼리 문자열, HTTP 헤더, 공격자가 응용 프로그램은 손상시키지 않은 경우라도 데이터는 손상되었을 수 있는 데이터베이스에서 가져온 데이터도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="6a026-113">신뢰할 수 없는 데이터를 HTML 요소에 삽입하기 전에 HTML로 인코딩되었는지 확인하십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="6a026-114">HTML 인코딩은 &lt;와 같은 문자를 사용 하 여 &amp;lt와 같은 안전한 형식으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="6a026-115">신뢰할 수 없는 데이터를 HTML 특성에 삽입하기 전에 HTML로 인코딩되었는지 확인하십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="6a026-116">HTML 특성 인코딩은 HTML 인코딩의 상위 집합으로, `"` 또는 `'`와 같은 추가 문자를 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="6a026-117">신뢰할 수 없는 데이터를 Javascript에 삽입하기 전에, 런타임에 콘텐츠를 가져오는 HTML 요소에 데이터를 배치하십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="6a026-118">배치할 수 없는 경우, 데이터가 JavaScript로 인코딩되었는지 확인하십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="6a026-119">Javascript 인코딩은 JavaScript에 대 한 위험한 문자를 사용 하 여 16 진수로 바꿉니다. 예를 들어 &lt; `\u003C`로 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="6a026-120">신뢰할 수 없는 데이터를 URL 쿼리 문자열에 삽입하기 전에 URL로 인코딩되었는지 확인하십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="6a026-121">Razor를 사용하여 HTML로 인코딩하기</span><span class="sxs-lookup"><span data-stu-id="6a026-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="6a026-122">MVC에서 사용되는 Razor 엔진은 변수를 통해 제공된 모든 출력을 자동으로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="6a026-123">*@* 지시어를 사용할 때마다 HTML 특성 인코딩 규칙을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="6a026-124">HTML 특성 인코딩은 HTML 인코딩의 상위 집합이므로 둘 중에 어떤 것을 사용해야 할 지 고민할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="6a026-125">신뢰할 수 없는 데이터를 JavaScript에 직접 삽입하지 말고 HTML 컨텍스트 내에서만 @ 지시문을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="6a026-126">태그 도우미는 또한 태그 매개 변수에 사용하는 입력값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="6a026-127">다음의 Razor 뷰를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="6a026-128">이 보기는 *Untrustedinput* 변수의 내용을 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="6a026-129">이 변수는 XSS 공격에 사용 되는 일부 문자 (&lt;"및 &gt;를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="6a026-130">이 소스의 렌더링 결과는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="6a026-131">ASP.NET Core MVC는 출력 시 자동으로 인코딩되지 않는 `HtmlString` 클래스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="6a026-132">XSS 취약점이 노출될 수 있으므로 절대로 신뢰할 수 없는 입력값과 함께 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="6a026-133">Razor를 사용하여 JavaScript로 인코딩하기</span><span class="sxs-lookup"><span data-stu-id="6a026-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="6a026-134">간혹 뷰 처리 과정에서 JavaScript에 값을 삽입하고 싶을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="6a026-135">이렇게 하는 데는 두 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-135">There are two ways to do this.</span></span> <span data-ttu-id="6a026-136">값을 삽입하는 가장 안전한 방법으로는 태그의 데이터 특성에 값을 삽입하고 JavaScript에서 가져오는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="6a026-137">예들 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-137">For example:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="6a026-138">인코딩 결과로 다음과 같은 HTML이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-138">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="6a026-139">실행하면 다음과 같은 렌더링 결과를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-139">Which, when it runs, will render the following:</span></span>

```
<"123">
   <"123">
```

<span data-ttu-id="6a026-140">또한 JavaScript 인코더를 직접 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-140">You can also call the JavaScript encoder directly:</span></span>

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

<span data-ttu-id="6a026-141">그러면 브라우저에서 다음과 같이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-141">This will render in the browser as follows:</span></span>

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> <span data-ttu-id="6a026-142">DOM 요소를 생성하기 위해 신뢰할 수 없는 입력값을 JavaScript와 연결하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="6a026-143">`createElement()`를 사용 하 고 `node.TextContent=`와 같은 속성 값을 적절 하 게 할당 해야 합니다. 그렇지 않으면 DOM 기반 XSS에 직접 노출 하는 `element.SetAttribute()`/`element[attribute]=` 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="6a026-144">코드에서 인코더에 액세스하기</span><span class="sxs-lookup"><span data-stu-id="6a026-144">Accessing encoders in code</span></span>

<span data-ttu-id="6a026-145">HTML, JavaScript 및 URL 인코더를 두 가지 방법으로 사용할 수 있습니다. [종속성 주입](xref:fundamentals/dependency-injection) 을 통해 코드를 삽입 하거나 `System.Text.Encodings.Web` 네임 스페이스에 포함 된 기본 인코더를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="6a026-146">기본 인코더를 사용하는 경우 가장 안전한 인코딩 규칙을 사용하므로 개발자가 직접 정의한 안전 문자 범위는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="6a026-147">DI를 통해 구성 가능한 인코더를 사용 하려면 생성자는 *htmlencoder*, *JavaScriptEncoder* 및 *urlencoder* 매개 변수를 적절 하 게 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="6a026-148">예를 들어,</span><span class="sxs-lookup"><span data-stu-id="6a026-148">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="6a026-149">URL 매개 변수 인코딩하기</span><span class="sxs-lookup"><span data-stu-id="6a026-149">Encoding URL Parameters</span></span>

<span data-ttu-id="6a026-150">신뢰할 수 없는 입력을 값으로 사용 하 여 URL 쿼리 문자열을 작성 하려는 경우 `UrlEncoder`를 사용 하 여 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="6a026-151">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="6a026-152">인코딩 후 Url-encodedvalue 변수는 `%22Quoted%20Value%20with%20spaces%20and%20%26%22`을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="6a026-153">공백, 따옴표, 마침표 및 기타 안전하지 않은 문자는 16진수 값으로 퍼센트 인코딩(즉, URL로 인코딩)됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="6a026-154">예를 들어, 공백 문자는 `%20`으로 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="6a026-155">신뢰할 수 없는 입력값은 항상 쿼리 문자열의 값으로 전달해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="6a026-156">사용자 지정 인코더 만들기</span><span class="sxs-lookup"><span data-stu-id="6a026-156">Customizing the Encoders</span></span>

<span data-ttu-id="6a026-157">기본적으로 인코더에서 사용하는 안전 목록은 기본 라틴 유니코드 범위에 한정되어 있으며, 이 외의 모든 문자는 해당 문자 코드로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="6a026-158">이러한 동작은 인코딩을 사용하여 문자열을 출력하는 Razor TagHelper와 HtmlHelper 렌더링에도 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="6a026-159">이는 알 수 없는 문제의 발생을 막거나 이전의 브라우저 버그는 영어 이외의 문자 처리를 기반으로 한 구문 분석에 방해가 되었기에 향후 발생할 수 있는 브라우저 버그를 막기 위한 조치입니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="6a026-160">한자, 키릴 문자와 같은 비 라틴계 문자를 자주 사용하는 웹사이트의 경우에는 불편할 수 있는 조치일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="6a026-161">`ConfigureServices()`시작 하는 동안 응용 프로그램에 적합 한 유니코드 범위를 포함 하도록 인코더 안전 목록을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="6a026-162">예를 들어 다음과 같은 기본 구성에서 Razor HtmlHelper를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="6a026-163">웹페이지에서 소스를 확인하면 중국어 텍스트가 다음과 같이 렌더링된 것을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="6a026-164">인코더가 안전 하 게 처리 하는 문자를 확장 하려면 `startup.cs`의 `ConfigureServices()` 메서드에 다음 줄을 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="6a026-165">이 예제에서는 `CjkUnifiedIdeographs`의 유니코드 범위를 포함하도록 안전 목록을 확장했습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="6a026-166">렌더링된 결과는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="6a026-167">안전 목록 범위는 언어가 아닌 유니코드 차트로서 명시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="6a026-168">[유니코드 표준](https://unicode.org/) 에는 문자를 포함 하는 차트를 찾는 데 사용할 수 있는 [코드 차트](https://www.unicode.org/charts/index.html) 목록이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="6a026-169">각 인코더(예: Html, JavaScript, Url)는 개별적으로 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="6a026-170">사용자 지정 안전 목록은 종속성 주입을 통해 생성된 인코더에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="6a026-171">`System.Text.Encodings.Web.*Encoder.Default`를 통해 인코더에 직접 액세스 하는 경우 기본 라틴어 전용 safelist가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="6a026-172">인코딩이 이뤄져야 할 시점</span><span class="sxs-lookup"><span data-stu-id="6a026-172">Where should encoding take place?</span></span>

<span data-ttu-id="6a026-173">일반적으로 인코딩은 출력 시점에 이루어져야 하며 인코딩한 값을 데이터베이스에 저장해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="6a026-174">출력 시점에서 인코딩하면 데이터의 용도를 변경(예: HTML에서 쿼리 문자열 값으로 변경)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="6a026-175">또한 검색 전 값을 인코딩 하지 않고도 데이터를 쉽게 검색할 수 있으며 인코더의 변경이나 버그 수정 시 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="6a026-176">XSS 방지 기술로서의 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="6a026-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="6a026-177">유효성 검사는 XSS 공격을 제한하는 유용한 도구가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="6a026-178">예를 들어 0~9 사이의 문자만 포함된 숫자 문자열은 XSS 공격을 일으키지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="6a026-179">이러한 검사 과정은 HTML을 통한 사용자 입력을 허용할 때 더욱 복잡해집니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="6a026-180">HTML 입력값을 구문 분석하는 것은 불가능하지는 않으나 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="6a026-181">Markdown과 같이 파서가 삽입된 HTML을 제거하는 기능은 다양한 값이 입력될 때 보다 안전한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="6a026-182">유효성 검사에만 의존하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="6a026-182">Never rely on validation alone.</span></span> <span data-ttu-id="6a026-183">어떠한 검증 과정이나 처리가 이루어졌다 하더라도 항상 출력 전에 신뢰할 수 없는 입력값을 인코딩하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="6a026-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
