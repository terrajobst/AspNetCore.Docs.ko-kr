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
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>ASP.NET Core에서 교차 사이트 스크립팅(XSS) 방지하기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

교차 사이트 스크립팅(XSS)은 공격자가 웹페이지에 Javascript와 같은 클라이언트측 스크립트를 웹페이지에 배치하도록 하는 보안상 취약점을 일컫습니다. 다른 사용자가 공격당한 페이지를 로드할 경우 쿠키 및 세션 토큰을 탈취하고 DOM을 조작하여 웹 페이지 내용을 변경하거나 다른 페이지로 이동시킬 수 있는 공격자의 스크립트가 실행됩니다. XSS 취약성으로 인 한 응용 프로그램은 사용자 입력 하 고 유효성 검사, 인코딩 또는 해 서 이스케이프 하지 않고 페이지를 출력 하는 경우에 일반적으로 발생 합니다. XSS 취약점은 응용 프로그램에서 사용자 입력을 받아 유효성 검사, 인코딩, 이스케이프 과정을 거치지 않고 페이지에 출력할 때 발생합니다.

## <a name="protecting-your-application-against-xss"></a>응용 프로그램을 XSS로부터 보호하기

기본 수준의 XSS는 응용 프로그램을 속여에 삽입 하 여 렌더링 된 페이지에 `<script>` 태그를 삽입 하거나 요소에 `On*` 이벤트를 삽입 하는 방식으로 작동 합니다. 개발자는 이러한 XSS 공격으로부터 응용 프로그램을 보호하기 위해 다음과 같은 조치를 취해야 합니다.

1. 아래의 단계를 거치지 않는 한, 신뢰할 수 없는 데이터를 HTML 입력에 추가하지 마십시오. 신뢰할 수 없는 데이터란 공격자가 제어할 수 있는 모든 데이터를 의미하며, HTML 폼 입력, 쿼리 문자열, HTTP 헤더, 공격자가 응용 프로그램은 손상시키지 않은 경우라도 데이터는 손상되었을 수 있는 데이터베이스에서 가져온 데이터도 포함됩니다.

2. 신뢰할 수 없는 데이터를 HTML 요소에 삽입하기 전에 HTML로 인코딩되었는지 확인하십시오. HTML 인코딩은 &lt;와 같은 문자를 사용 하 여 &amp;lt와 같은 안전한 형식으로 변경 합니다.

3. 신뢰할 수 없는 데이터를 HTML 특성에 삽입하기 전에 HTML로 인코딩되었는지 확인하십시오. HTML 특성 인코딩은 HTML 인코딩의 상위 집합으로, `"` 또는 `'`와 같은 추가 문자를 인코딩합니다.

4. 신뢰할 수 없는 데이터를 Javascript에 삽입하기 전에, 런타임에 콘텐츠를 가져오는 HTML 요소에 데이터를 배치하십시오. 배치할 수 없는 경우, 데이터가 JavaScript로 인코딩되었는지 확인하십시오. Javascript 인코딩은 JavaScript에 대 한 위험한 문자를 사용 하 여 16 진수로 바꿉니다. 예를 들어 &lt; `\u003C`로 인코딩됩니다.

5. 신뢰할 수 없는 데이터를 URL 쿼리 문자열에 삽입하기 전에 URL로 인코딩되었는지 확인하십시오.

## <a name="html-encoding-using-razor"></a>Razor를 사용하여 HTML로 인코딩하기

MVC에서 사용되는 Razor 엔진은 변수를 통해 제공된 모든 출력을 자동으로 인코딩합니다. *@* 지시어를 사용할 때마다 HTML 특성 인코딩 규칙을 사용 합니다. HTML 특성 인코딩은 HTML 인코딩의 상위 집합이므로 둘 중에 어떤 것을 사용해야 할 지 고민할 필요가 없습니다. 신뢰할 수 없는 데이터를 JavaScript에 직접 삽입하지 말고 HTML 컨텍스트 내에서만 @ 지시문을 사용하십시오. 태그 도우미는 또한 태그 매개 변수에 사용하는 입력값을 인코딩합니다.

다음의 Razor 뷰를 살펴보겠습니다.

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

이 보기는 *Untrustedinput* 변수의 내용을 출력 합니다. 이 변수는 XSS 공격에 사용 되는 일부 문자 (&lt;"및 &gt;를 포함 합니다. 이 소스의 렌더링 결과는 다음과 같습니다.

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC는 출력 시 자동으로 인코딩되지 않는 `HtmlString` 클래스를 제공 합니다. XSS 취약점이 노출될 수 있으므로 절대로 신뢰할 수 없는 입력값과 함께 사용하지 마십시오.

## <a name="javascript-encoding-using-razor"></a>Razor를 사용하여 JavaScript로 인코딩하기

간혹 뷰 처리 과정에서 JavaScript에 값을 삽입하고 싶을 수 있습니다. 이렇게 하는 데는 두 가지 방법이 있습니다. 값을 삽입하는 가장 안전한 방법으로는 태그의 데이터 특성에 값을 삽입하고 JavaScript에서 가져오는 것입니다. 예들 들어 다음과 같습니다.

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

인코딩 결과로 다음과 같은 HTML이 생성됩니다.

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

실행하면 다음과 같은 렌더링 결과를 얻을 수 있습니다.

```
<"123">
   <"123">
```

또한 JavaScript 인코더를 직접 호출할 수도 있습니다.

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

그러면 브라우저에서 다음과 같이 렌더링됩니다.

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> DOM 요소를 생성하기 위해 신뢰할 수 없는 입력값을 JavaScript와 연결하지 마십시오. `createElement()`를 사용 하 고 `node.TextContent=`와 같은 속성 값을 적절 하 게 할당 해야 합니다. 그렇지 않으면 DOM 기반 XSS에 직접 노출 하는 `element.SetAttribute()`/`element[attribute]=` 사용 합니다.

## <a name="accessing-encoders-in-code"></a>코드에서 인코더에 액세스하기

HTML, JavaScript 및 URL 인코더를 두 가지 방법으로 사용할 수 있습니다. [종속성 주입](xref:fundamentals/dependency-injection) 을 통해 코드를 삽입 하거나 `System.Text.Encodings.Web` 네임 스페이스에 포함 된 기본 인코더를 사용할 수 있습니다. 기본 인코더를 사용하는 경우 가장 안전한 인코딩 규칙을 사용하므로 개발자가 직접 정의한 안전 문자 범위는 적용되지 않습니다.

DI를 통해 구성 가능한 인코더를 사용 하려면 생성자는 *htmlencoder*, *JavaScriptEncoder* 및 *urlencoder* 매개 변수를 적절 하 게 사용 해야 합니다. 예를 들어,

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

## <a name="encoding-url-parameters"></a>URL 매개 변수 인코딩하기

신뢰할 수 없는 입력을 값으로 사용 하 여 URL 쿼리 문자열을 작성 하려는 경우 `UrlEncoder`를 사용 하 여 값을 인코딩합니다. 예를 들면 다음과 같습니다.

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

인코딩 후 Url-encodedvalue 변수는 `%22Quoted%20Value%20with%20spaces%20and%20%26%22`을 포함 합니다. 공백, 따옴표, 마침표 및 기타 안전하지 않은 문자는 16진수 값으로 퍼센트 인코딩(즉, URL로 인코딩)됩니다.

>[!WARNING]
> 예를 들어, 공백 문자는 `%20`으로 인코딩됩니다. 신뢰할 수 없는 입력값은 항상 쿼리 문자열의 값으로 전달해야 합니다.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>사용자 지정 인코더 만들기

기본적으로 인코더에서 사용하는 안전 목록은 기본 라틴 유니코드 범위에 한정되어 있으며, 이 외의 모든 문자는 해당 문자 코드로 인코딩합니다. 이러한 동작은 인코딩을 사용하여 문자열을 출력하는 Razor TagHelper와 HtmlHelper 렌더링에도 영향을 줍니다.

이는 알 수 없는 문제의 발생을 막거나 이전의 브라우저 버그는 영어 이외의 문자 처리를 기반으로 한 구문 분석에 방해가 되었기에 향후 발생할 수 있는 브라우저 버그를 막기 위한 조치입니다. 한자, 키릴 문자와 같은 비 라틴계 문자를 자주 사용하는 웹사이트의 경우에는 불편할 수 있는 조치일 수 있습니다.

`ConfigureServices()`시작 하는 동안 응용 프로그램에 적합 한 유니코드 범위를 포함 하도록 인코더 안전 목록을 사용자 지정할 수 있습니다.

예를 들어 다음과 같은 기본 구성에서 Razor HtmlHelper를 사용할 수 있습니다.

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

웹페이지에서 소스를 확인하면 중국어 텍스트가 다음과 같이 렌더링된 것을 확인할 수 있습니다.

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

인코더가 안전 하 게 처리 하는 문자를 확장 하려면 `startup.cs`의 `ConfigureServices()` 메서드에 다음 줄을 삽입 합니다.

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

이 예제에서는 `CjkUnifiedIdeographs`의 유니코드 범위를 포함하도록 안전 목록을 확장했습니다. 렌더링된 결과는 다음과 같습니다.

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

안전 목록 범위는 언어가 아닌 유니코드 차트로서 명시되어 있습니다. [유니코드 표준](https://unicode.org/) 에는 문자를 포함 하는 차트를 찾는 데 사용할 수 있는 [코드 차트](https://www.unicode.org/charts/index.html) 목록이 있습니다. 각 인코더(예: Html, JavaScript, Url)는 개별적으로 구성해야 합니다.

> [!NOTE]
> 사용자 지정 안전 목록은 종속성 주입을 통해 생성된 인코더에만 적용됩니다. `System.Text.Encodings.Web.*Encoder.Default`를 통해 인코더에 직접 액세스 하는 경우 기본 라틴어 전용 safelist가 사용 됩니다.

## <a name="where-should-encoding-take-place"></a>인코딩이 이뤄져야 할 시점

일반적으로 인코딩은 출력 시점에 이루어져야 하며 인코딩한 값을 데이터베이스에 저장해서는 안 됩니다. 출력 시점에서 인코딩하면 데이터의 용도를 변경(예: HTML에서 쿼리 문자열 값으로 변경)할 수 있습니다. 또한 검색 전 값을 인코딩 하지 않고도 데이터를 쉽게 검색할 수 있으며 인코더의 변경이나 버그 수정 시 활용할 수 있습니다.

## <a name="validation-as-an-xss-prevention-technique"></a>XSS 방지 기술로서의 유효성 검사

유효성 검사는 XSS 공격을 제한하는 유용한 도구가 될 수 있습니다. 예를 들어 0~9 사이의 문자만 포함된 숫자 문자열은 XSS 공격을 일으키지 않습니다. 이러한 검사 과정은 HTML을 통한 사용자 입력을 허용할 때 더욱 복잡해집니다. HTML 입력값을 구문 분석하는 것은 불가능하지는 않으나 어렵습니다. Markdown과 같이 파서가 삽입된 HTML을 제거하는 기능은 다양한 값이 입력될 때 보다 안전한 방법입니다. 유효성 검사에만 의존하지 마십시오. 어떠한 검증 과정이나 처리가 이루어졌다 하더라도 항상 출력 전에 신뢰할 수 없는 입력값을 인코딩하시기 바랍니다.
