---
title: ASP.NET Core에 대한 Razor 구문 참조
author: rick-anderson
description: 웹 페이지에 서버 기반 코드를 포함하는 Razor 태그 구문에 대해 알아봅니다.
ms.author: riande
ms.date: 06/12/2019
uid: mvc/views/razor
ms.openlocfilehash: 87c5b97a653c139b8b79f4270e0d9d0081815433
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034944"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="829f5-103">ASP.NET Core에 대한 Razor 구문 참조</span><span class="sxs-lookup"><span data-stu-id="829f5-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="829f5-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen) 및 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="829f5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="829f5-105">Razor는 웹 페이지에 서버 기반 코드를 포함하는 태그 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="829f5-106">Razor 구문은 Razor 태그, C# 및 HTML로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="829f5-107">Razor를 포함하는 파일의 확장명은 일반적으로 *.cshtml*입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-107">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="829f5-108">HTML 렌더링</span><span class="sxs-lookup"><span data-stu-id="829f5-108">Rendering HTML</span></span>

<span data-ttu-id="829f5-109">기본 Razor 언어는 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-109">The default Razor language is HTML.</span></span> <span data-ttu-id="829f5-110">Razor 태그에서 HTML을 렌더링하는 방법은 HTML 파일에서 HTML을 렌더링하는 방법과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-110">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="829f5-111">*.cshtml* Razor 파일의 HTML 태그는 변경되지 않은 서버에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-111">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="829f5-112">Razor 구문</span><span class="sxs-lookup"><span data-stu-id="829f5-112">Razor syntax</span></span>

<span data-ttu-id="829f5-113">Razor는 C#을 지원하며 `@` 기호를 사용하여 HTML에서 C#으로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-113">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="829f5-114">Razor는 C# 식을 평가하여 HTML 출력에서 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-114">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="829f5-115">`@` 기호 뒤에 [Razor 예약 키워드](#razor-reserved-keywords)가 사용되면 이 기호는 Razor 관련 태그로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-115">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="829f5-116">그렇지 않으면 C#으로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-116">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="829f5-117">Razor 태그에서 `@` 기호를 이스케이프하려면 두 번째 `@` 기호를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-117">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="829f5-118">코드는 단일 `@` 기호를 사용하여 HTML로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-118">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="829f5-119">이메일 주소를 포함하는 HTML 특성 및 콘텐츠는 `@` 기호를 전환 문자로 취급하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-119">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="829f5-120">다음 예제의 이메일 주소는 Razor 구문 분석에 의해 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-120">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="829f5-121">암시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="829f5-121">Implicit Razor expressions</span></span>

<span data-ttu-id="829f5-122">암시적 Razor 식은 `@`으로 시작하고 그 뒤에 C# 코드가 옵니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-122">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="829f5-123">C# `await` 키워드를 제외하고, 암시적 식에 공백이 있으면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-123">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="829f5-124">C# 문에 명확한 끝이 있으면 공백을 혼합 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-124">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="829f5-125">암시적 식은 C# 제네릭을 포함할 수 **없습니다**. 대괄호(`<>`) 안에 있는 문자가 HTML 태그로 해석되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-125">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="829f5-126">다음 코드는 유효하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="829f5-126">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="829f5-127">위의 코드는 다음 중 하나와 비슷한 컴파일러 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-127">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="829f5-128">"Int" 요소가 종료되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-128">The "int" element wasn't closed.</span></span> <span data-ttu-id="829f5-129">모든 요소는 하나 있어야 자체적으로 닫히거나 일치하는 끝 태그가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-129">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="829f5-130">'GenericMethod' 메서드 그룹을 비대리자 형식 '개체'로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-130">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="829f5-131">메서드를 호출할 생각이었나요?</span><span class="sxs-lookup"><span data-stu-id="829f5-131">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="829f5-132">제네릭 메서드 호출은 [명시적 Razor 식](#explicit-razor-expressions) 또는 [Razor 코드 블록](#razor-code-blocks)에 래핑되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-132">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="829f5-133">명시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="829f5-133">Explicit Razor expressions</span></span>

<span data-ttu-id="829f5-134">명시적 Razor 식은 `@` 기호와 균형 잡힌 괄호로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-134">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="829f5-135">지난 주의 시간을 렌더링하기 위해 다음 Razor 태그가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-135">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="829f5-136">`@()` 괄호 안의 모든 콘텐츠가 평가되고 출력에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-136">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="829f5-137">이전 섹션에서 설명한 암시적 식은 일반적으로 공백을 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-137">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="829f5-138">다음 코드에서 일주일은 현재 시간에서 차감되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-138">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="829f5-139">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-139">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="829f5-140">명시적 식은 텍스트를 식 결과와 연결하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-140">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="829f5-141">명시적 식이 없으면 `<p>Age@joe.Age</p>`는 이메일 주소로 처리되고 `<p>Age@joe.Age</p>`가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-141">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="829f5-142">명시적 식으로 작성되면 `<p>Age33</p>`이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-142">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="829f5-143">명시적 식은 *.cshtml* 파일에 있는 제네릭 메서드의 출력을 렌더링하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-143">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="829f5-144">다음 표시는 앞에서 C# 제네릭의 대괄호로 인해 발생한 오류를 해결하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-144">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="829f5-145">이 코드는 명시적 식으로 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-145">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="829f5-146">식 인코딩</span><span class="sxs-lookup"><span data-stu-id="829f5-146">Expression encoding</span></span>

<span data-ttu-id="829f5-147">문자열로 확인되는 C# 식은 인코딩된 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-147">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="829f5-148">`IHtmlContent`로 확인되는 C# 식은 `IHtmlContent.WriteTo`를 통해 직접 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-148">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="829f5-149">`IHtmlContent`로 확인되지 않는 C# 식은 `ToString`을 통해 문자열로 변환되고 인코딩된 후 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-149">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="829f5-150">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-150">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="829f5-151">다음과 같은 이유로 HTML은 브라우저에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-151">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="829f5-152">`HtmlHelper.Raw` 출력은 인코딩되지 않지만 HTML 태그로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-152">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="829f5-153">삭제되지 않은 사용자 입력에서 `HtmlHelper.Raw`를 사용하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-153">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="829f5-154">사용자 입력에 악의적인 JavaScript 또는 다른 악용 기법이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-154">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="829f5-155">사용자 입력을 제거하기는 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-155">Sanitizing user input is difficult.</span></span> <span data-ttu-id="829f5-156">사용자 입력에 `HtmlHelper.Raw`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="829f5-156">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="829f5-157">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-157">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="829f5-158">Razor 코드 블록</span><span class="sxs-lookup"><span data-stu-id="829f5-158">Razor code blocks</span></span>

<span data-ttu-id="829f5-159">Razor 코드 블록은 `@`으로 시작하고 `{}`로 묶입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-159">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="829f5-160">식과는 달리, 코드 블록 내부의 C# 코드는 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-160">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="829f5-161">보기의 코드 블록과 식은 같은 범위를 공유하고 순서대로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-161">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="829f5-162">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-162">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="829f5-163">코드 블록에서 템플릿 메서드로 제공되는 태그를 사용하여 [로컬 함수](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-163">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="829f5-164">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-164">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="829f5-165">암시적 전환</span><span class="sxs-lookup"><span data-stu-id="829f5-165">Implicit transitions</span></span>

<span data-ttu-id="829f5-166">코드 블록의 기본 언어는 C#이지만, Razor 페이지를 다시 HTML로 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-166">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="829f5-167">구분 기호로 분리된 명시적 전환</span><span class="sxs-lookup"><span data-stu-id="829f5-167">Explicit delimited transition</span></span>

<span data-ttu-id="829f5-168">HTML을 렌더링해야 하는 코드 블록의 하위 섹션을 정의하려면 렌더링할 문자를 Razor **\<text>** 태그로 묶어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-168">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor **\<text>** tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="829f5-169">HTML 태그로 묶이지 않은 HTML을 렌더링하려면 이 방법을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="829f5-169">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="829f5-170">HTML 또는 Razor 태그가 없으면 Razor 런타임 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-170">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="829f5-171">**\<text>** 태그는 콘텐츠를 렌더링할 때 공백을 제어하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-171">The **\<text>** tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="829f5-172">**\<text>** 태그 사이의 콘텐츠만 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-172">Only the content between the **\<text>** tag is rendered.</span></span>
* <span data-ttu-id="829f5-173">**\<text>** 태그 앞 또는 뒤에 있는 공백은 HTML 출력에 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-173">No whitespace before or after the **\<text>** tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="829f5-174">@을 사용하여 명시적 줄 전환:</span><span class="sxs-lookup"><span data-stu-id="829f5-174">Explicit Line Transition with @:</span></span>

<span data-ttu-id="829f5-175">코드 블록 내부의 나머지 전체 줄을 HTML로 렌더링하려면 `@:` 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-175">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="829f5-176">코드에 `@:`이 없으면 Razor 런타임 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-176">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="829f5-177">경고: Razor 파일의 추가 `@` 문자는 블록의 뒷부분에 나오는 명령문에서 컴파일러 오류를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-177">Warning: Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="829f5-178">실제 오류가 보고된 오류보다 먼저 발생하기 때문에 이러한 컴파일러 오류를 이해하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-178">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="829f5-179">이 오류는 여러 암시적/명시적 식을 단일 코드 블록에 결합한 이후에 자주 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-179">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="829f5-180">제어 구조</span><span class="sxs-lookup"><span data-stu-id="829f5-180">Control structures</span></span>

<span data-ttu-id="829f5-181">제어 구조는 코드 블록의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-181">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="829f5-182">코드 블록의 모든 측면(태그로 전환, 인라인 C#)은 다음 구조에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-182">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="829f5-183">조건부 @if, else if, else 및 @switch</span><span class="sxs-lookup"><span data-stu-id="829f5-183">Conditionals @if, else if, else, and @switch</span></span>

<span data-ttu-id="829f5-184">`@if`는 코드가 실행되는 시기를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-184">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="829f5-185">`else` 및 `else if`는 `@` 기호가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-185">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="829f5-186">다음 태그는 switch 문 사용 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-186">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="829f5-187">Looping @for, @foreach, @while 및 @do while</span><span class="sxs-lookup"><span data-stu-id="829f5-187">Looping @for, @foreach, @while, and @do while</span></span>

<span data-ttu-id="829f5-188">템플릿 기반 HTML은 반복 제어 문으로 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-188">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="829f5-189">사람 목록을 렌더링하려면:</span><span class="sxs-lookup"><span data-stu-id="829f5-189">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="829f5-190">다음 반복 문이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-190">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="829f5-191">복합 @using</span><span class="sxs-lookup"><span data-stu-id="829f5-191">Compound @using</span></span>

<span data-ttu-id="829f5-192">C#에서 `using` 문은 개체가 삭제되도록 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-192">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="829f5-193">Razor에서는 동일한 메커니즘을 사용하여 추가 콘텐츠를 포함하는 HTML 도우미를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-193">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="829f5-194">다음 코드에서 HTML 도우미는 `@using` 문을 사용하여 form 태그를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-194">In the following code, HTML Helpers render a form tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

<span data-ttu-id="829f5-195">범위 수준 작업은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하여 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-195">Scope-level actions can be performed with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="829f5-196">@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="829f5-196">@try, catch, finally</span></span>

<span data-ttu-id="829f5-197">예외 처리는 C#과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

<span data-ttu-id="829f5-198">Razor는 lock 문을 사용하여 중요한 섹션을 보호하는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="829f5-199">설명</span><span class="sxs-lookup"><span data-stu-id="829f5-199">Comments</span></span>

<span data-ttu-id="829f5-200">Razor는 C# 및 HTML 주석을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="829f5-201">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="829f5-202">Razor 주석은 웹 페이지가 렌더링되기 전에 서버에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="829f5-203">Razor는 `@*  *@`을 사용하여 주석을 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="829f5-204">다음 코드는 주석 처리되며, 따라서 서버에서 어떤 태그도 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="829f5-205">지시문</span><span class="sxs-lookup"><span data-stu-id="829f5-205">Directives</span></span>

<span data-ttu-id="829f5-206">Razor 지시문은 `@` 기호 뒤에 예약된 키워드를 사용하여 암시적 식으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="829f5-207">지시문은 일반적으로 보기를 구문 분석하는 방식을 변경하거나 다른 기능을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="829f5-208">Razor가 보기에 대한 코드를 생성하는 원리를 이해하면 지시문의 작동 원리를 쉽게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="829f5-209">이 코드는 다음과 비슷한 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-209">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="829f5-210">이 문서의 뒷부분에 나오는 [보기용으로 생성된 Razor C# 클래스 검사](#inspect-the-razor-c-class-generated-for-a-view) 섹션에서는 생성된 이 클래스를 보는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

<a name="using"></a>

### <a name="using"></a>@using

<span data-ttu-id="829f5-211">`@using` 지시문은 C# `using` 지시문을 생성된 보기에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-211">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

<span data-ttu-id="829f5-212">`@model` 지시문은 보기에 전달되는 모델 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-212">The `@model` directive specifies the type of the model passed to a view:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="829f5-213">개별 사용자 계정을 사용하여 만든 ASP.NET Core MVC 앱에서, *Views/Account/Login.cshtml* 보기는 다음과 같은 모델 선언을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-213">In an ASP.NET Core MVC app created with individual user accounts, the *Views/Account/Login.cshtml* view contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="829f5-214">생성된 클래스는 `RazorPage<dynamic>`에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-214">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="829f5-215">Razor는 보기에 전달된 모델에 액세스할 수 있는 `Model` 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-215">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="829f5-216">`@model` 지시문은 이 속성의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-216">The `@model` directive specifies the type of this property.</span></span> <span data-ttu-id="829f5-217">이 지시문은 보기가 파생되는 클래스를 생성한 `RazorPage<T>`의 `T`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-217">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="829f5-218">`@model` 지시문이 지정되지 않을 경우 `Model` 속성은 `dynamic` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-218">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="829f5-219">모델의 값은 컨트롤러에서 보기로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-219">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="829f5-220">자세한 내용은 [강력한 형식의 모델 및 &commat;모델 키워드](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="829f5-220">For more information, see [Strongly typed models and the &commat;model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="inherits"></a>@inherits

<span data-ttu-id="829f5-221">`@inherits` 지시문은 보기에서 상속하는 클래스에 대한 완전한 제어권을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-221">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="829f5-222">다음 코드는 사용자 지정 Razor 페이지 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-222">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="829f5-223">`CustomText`는 보기에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-223">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="829f5-224">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-224">The code renders the following HTML:</span></span>

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 <span data-ttu-id="829f5-225">`@model` 및 `@inherits`는 동일한 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-225">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="829f5-226">`@inherits`는 보기에서 가져오는 *_ViewImports.cshtml* 파일에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-226">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="829f5-227">다음 코드는 강력한 형식의 보기 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-227">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="829f5-228">"rick@contoso.com"이 모델에 전달되면 보기에서 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-228">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

<span data-ttu-id="829f5-229">`@inject` 지시문을 사용하면 Razor 페이지에서 [서비스 컨테이너](xref:fundamentals/dependency-injection)의 서비스를 보기에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-229">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="829f5-230">자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="829f5-230">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

### <a name="functions"></a>@functions

<span data-ttu-id="829f5-231">`@functions` 지시문을 사용하면 Razor 페이지에서 C# 코드 블록을 보기에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-231">The `@functions` directive enables a Razor Page to add a C# code block to a view:</span></span>

```cshtml
@functions { // C# Code }
```

<span data-ttu-id="829f5-232">예:</span><span class="sxs-lookup"><span data-stu-id="829f5-232">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="829f5-233">이 코드는 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-233">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="829f5-234">다음 코드는 생성된 Razor C# 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-234">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="829f5-235">`@functions` 메서드는 태그가 있을 때 템플릿 메서드로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-235">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="829f5-236">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-236">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="attribute"></a>@attribute

<span data-ttu-id="829f5-237">`@attribute` 지시문은 지정된 특성을 생성된 페이지 또는 보기의 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-237">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="829f5-238">다음 예제에서는 `[Authorize]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-238">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

> [!WARNING]
> <span data-ttu-id="829f5-239">ASP.NET Core 3.0 미리 보기 6 릴리스에서 `@attribute` 지시문이 *\_Imports.razor* 및 *\_ViewImports.cshtml* 파일에서 작동하지 않는 알려진 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-239">In ASP.NET Core 3.0 Preview 6 release, there's a known issue where `@attribute` directives don't work in *\_Imports.razor* and *\_ViewImports.cshtml* files.</span></span> <span data-ttu-id="829f5-240">이 문제는 미리 보기 7 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-240">This will be addressed in the Preview 7 release.</span></span>

### <a name="namespace"></a>@namespace

<span data-ttu-id="829f5-241">`@namespace` 지시문은 생성된 페이지 또는 보기 클래스의 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-241">The `@namespace` directive sets the namespace of the class of the generated page or view:</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="829f5-242">페이지 또는 보기가 `@namespace` 지시문을 사용하여 API를 가져오는 경우 원래 파일의 네임스페이스는 해당 네임스페이스를 기준으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-242">If a page or view imports API with an `@namespace` directive, the original file's namespace is set relative to that namespace.</span></span> 

<span data-ttu-id="829f5-243">*MyApp/Pages/\_ViewImports.cshtml*이 `@namespace Hello.World`를 포함하는 경우 `Hello.World` 네임스페이스를 가져오는 페이지 또는 보기의 네임스페이스는 다음 표와 같이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-243">If *MyApp/Pages/\_ViewImports.cshtml* contains `@namespace Hello.World`, the namespace of pages or views that import the `Hello.World` namespace is set as shown in the following table.</span></span>

| <span data-ttu-id="829f5-244">페이지(또는 보기)</span><span class="sxs-lookup"><span data-stu-id="829f5-244">Page (or view)</span></span>                     | <span data-ttu-id="829f5-245">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="829f5-245">Namespace</span></span>               |
| ---------------------------------- | ----------------------- |
| <span data-ttu-id="829f5-246">*MyApp/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="829f5-246">*MyApp/Pages/Index.cshtml*</span></span>         | `Hello.World`           |
| <span data-ttu-id="829f5-247">*MyApp/Pages/MorePages/Bar.cshtml*</span><span class="sxs-lookup"><span data-stu-id="829f5-247">*MyApp/Pages/MorePages/Bar.cshtml*</span></span> | `Hello.World.MorePages` |

<span data-ttu-id="829f5-248">여러 가져오기 파일에 `@namespace` 지시문이 있는 경우 디렉터리 체인의 페이지 또는 보기에 가장 가까운 파일이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-248">If multiple import files have the `@namespace` directive, the file closest to the page or view in the directory chain is used.</span></span>

### <a name="section"></a>@section

<span data-ttu-id="829f5-249">`@section` 지시문은 [layout](xref:mvc/views/layout)과 함께 사용되어 페이지 또는 보기에서 HTML 페이지의 여러 부분에 있는 콘텐츠를 렌더링할 수 있게 해줍니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-249">The `@section` directive is used in conjunction with the [layout](xref:mvc/views/layout) to enable pages or views to render content in different parts of the HTML page.</span></span> <span data-ttu-id="829f5-250">자세한 내용은 [섹션](xref:mvc/views/layout#layout-sections-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="829f5-250">For more information, see [Sections](xref:mvc/views/layout#layout-sections-label).</span></span>

## <a name="templated-razor-delegates"></a><span data-ttu-id="829f5-251">템플릿에 작성된 Razor 대리자</span><span class="sxs-lookup"><span data-stu-id="829f5-251">Templated Razor delegates</span></span>

<span data-ttu-id="829f5-252">Razor 템플릿을 사용하면 UI 코드 조각을 다음 형식으로 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-252">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="829f5-253">다음 예제에서는 템플릿에 작성된 Razor 대리자를 <xref:System.Func%602>로 지정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-253">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="829f5-254">[dynamic 형식](/dotnet/csharp/programming-guide/types/using-type-dynamic)은 대리자에서 캡슐화하는 메서드의 매개 변수로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-254">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="829f5-255">[object 형식](/dotnet/csharp/language-reference/keywords/object)은 대리자의 반환 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-255">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="829f5-256">템플릿은 `Name` 속성이 있는 `Pet`의 <xref:System.Collections.Generic.List%601>에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-256">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="829f5-257">템플릿은 `foreach` 문에서 제공하는 `pets`에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-257">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="829f5-258">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="829f5-258">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="829f5-259">또한 인라인 Razor 템플릿은 메서드에 대한 인수로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-259">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="829f5-260">다음 예제에서는 `Repeat` 메서드에서 Razor 템플릿을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-260">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="829f5-261">이 메서드는 템플릿을 사용하여 목록에서 제공된 항목의 반복이 포함된 HTML 콘텐츠를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-261">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="829f5-262">이전 예제의 애완 동물 목록을 사용하여 `Repeat` 메서드는 다음 항목과 함께 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-262">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="829f5-263"><xref:System.Collections.Generic.List%601>의 `Pet`입니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-263"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="829f5-264">각 애완 동물에 대한 반복 횟수</span><span class="sxs-lookup"><span data-stu-id="829f5-264">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="829f5-265">순서가 지정되지 않은 목록의 목록 항목에 사용할 인라인 템플릿</span><span class="sxs-lookup"><span data-stu-id="829f5-265">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="829f5-266">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="829f5-266">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="829f5-267">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="829f5-267">Tag Helpers</span></span>

<span data-ttu-id="829f5-268">[태그 도우미](xref:mvc/views/tag-helpers/intro)와 관련된 세 가지 지시문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-268">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="829f5-269">지시문</span><span class="sxs-lookup"><span data-stu-id="829f5-269">Directive</span></span> | <span data-ttu-id="829f5-270">함수</span><span class="sxs-lookup"><span data-stu-id="829f5-270">Function</span></span> |
| --------- | -------- |
| [<span data-ttu-id="829f5-271">&commat;addTagHelper</span><span class="sxs-lookup"><span data-stu-id="829f5-271">&commat;addTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="829f5-272">보기에 태그 도우미를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-272">Makes Tag Helpers available to a view.</span></span> |
| [<span data-ttu-id="829f5-273">&commat;removeTagHelper</span><span class="sxs-lookup"><span data-stu-id="829f5-273">&commat;removeTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="829f5-274">보기에서 이전에 추가된 태그 도우미를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-274">Removes Tag Helpers previously added from a view.</span></span> |
| [<span data-ttu-id="829f5-275">&commat;tagHelperPrefix</span><span class="sxs-lookup"><span data-stu-id="829f5-275">&commat;tagHelperPrefix</span></span>](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="829f5-276">태그 도우미를 지원하고 태그 도우미 사용을 명시적으로 만들어주는 태그 접두사를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-276">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="829f5-277">Razor 예약 키워드</span><span class="sxs-lookup"><span data-stu-id="829f5-277">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="829f5-278">Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="829f5-278">Razor keywords</span></span>

* <span data-ttu-id="829f5-279">페이지(ASP.NET Core 2.0 이상 필요)</span><span class="sxs-lookup"><span data-stu-id="829f5-279">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="829f5-280">네임스페이스(namespace)</span><span class="sxs-lookup"><span data-stu-id="829f5-280">namespace</span></span>
* <span data-ttu-id="829f5-281">함수</span><span class="sxs-lookup"><span data-stu-id="829f5-281">functions</span></span>
* <span data-ttu-id="829f5-282">상속</span><span class="sxs-lookup"><span data-stu-id="829f5-282">inherits</span></span>
* <span data-ttu-id="829f5-283">model</span><span class="sxs-lookup"><span data-stu-id="829f5-283">model</span></span>
* <span data-ttu-id="829f5-284">section</span><span class="sxs-lookup"><span data-stu-id="829f5-284">section</span></span>
* <span data-ttu-id="829f5-285">도우미(현재 ASP.NET Core에서 지원되지 않음)</span><span class="sxs-lookup"><span data-stu-id="829f5-285">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="829f5-286">Razor 키워드는 `@(Razor Keyword)`으로 이스케이프됩니다(예: `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="829f5-286">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="829f5-287">C# Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="829f5-287">C# Razor keywords</span></span>

* <span data-ttu-id="829f5-288">case</span><span class="sxs-lookup"><span data-stu-id="829f5-288">case</span></span>
* <span data-ttu-id="829f5-289">do</span><span class="sxs-lookup"><span data-stu-id="829f5-289">do</span></span>
* <span data-ttu-id="829f5-290">default</span><span class="sxs-lookup"><span data-stu-id="829f5-290">default</span></span>
* <span data-ttu-id="829f5-291">for</span><span class="sxs-lookup"><span data-stu-id="829f5-291">for</span></span>
* <span data-ttu-id="829f5-292">foreach</span><span class="sxs-lookup"><span data-stu-id="829f5-292">foreach</span></span>
* <span data-ttu-id="829f5-293">if</span><span class="sxs-lookup"><span data-stu-id="829f5-293">if</span></span>
* <span data-ttu-id="829f5-294">else</span><span class="sxs-lookup"><span data-stu-id="829f5-294">else</span></span>
* <span data-ttu-id="829f5-295">lock</span><span class="sxs-lookup"><span data-stu-id="829f5-295">lock</span></span>
* <span data-ttu-id="829f5-296">switch</span><span class="sxs-lookup"><span data-stu-id="829f5-296">switch</span></span>
* <span data-ttu-id="829f5-297">try</span><span class="sxs-lookup"><span data-stu-id="829f5-297">try</span></span>
* <span data-ttu-id="829f5-298">catch</span><span class="sxs-lookup"><span data-stu-id="829f5-298">catch</span></span>
* <span data-ttu-id="829f5-299">finally</span><span class="sxs-lookup"><span data-stu-id="829f5-299">finally</span></span>
* <span data-ttu-id="829f5-300">using</span><span class="sxs-lookup"><span data-stu-id="829f5-300">using</span></span>
* <span data-ttu-id="829f5-301">while</span><span class="sxs-lookup"><span data-stu-id="829f5-301">while</span></span>

<span data-ttu-id="829f5-302">C# Razor 키워드는 `@(@C# Razor Keyword)`으로 이중 이스케이프되어야 합니다(예: `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="829f5-302">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="829f5-303">첫 번째 `@`은 Razor 파서를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-303">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="829f5-304">두 번째 `@`은 C# 파서를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-304">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="829f5-305">Razor에서 사용하지 않는 예약된 키워드</span><span class="sxs-lookup"><span data-stu-id="829f5-305">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="829f5-306">클래스</span><span class="sxs-lookup"><span data-stu-id="829f5-306">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="829f5-307">보기용으로 생성된 Razor C# 클래스 검사</span><span class="sxs-lookup"><span data-stu-id="829f5-307">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="829f5-308">.NET Core SDK 2.1 이상에서는 [Razor SDK](xref:razor-pages/sdk)가 Razor 파일의 컴파일을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-308">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="829f5-309">프로젝트를 빌드할 때 Razor SDK는 프로젝트 루트에 *obj/<build_configuration>/<target_framework_moniker>/Razor* 디렉터리를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-309">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="829f5-310">*Razor* 디렉터리 내의 디렉터리 구조는 프로젝트의 디렉터리 구조를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-310">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="829f5-311">.NET Core 2.1을 대상으로 하는 ASP.NET Core 2.1 Razor Pages 프로젝트에서 다음 디렉터리 구조를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-311">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="829f5-312">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="829f5-312">**Areas/**</span></span>
  * <span data-ttu-id="829f5-313">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="829f5-313">**Admin/**</span></span>
    * <span data-ttu-id="829f5-314">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="829f5-314">**Pages/**</span></span>
      * <span data-ttu-id="829f5-315">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="829f5-315">*Index.cshtml*</span></span>
      * <span data-ttu-id="829f5-316">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="829f5-316">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="829f5-317">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="829f5-317">**Pages/**</span></span>
  * <span data-ttu-id="829f5-318">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="829f5-318">**Shared/**</span></span>
    * <span data-ttu-id="829f5-319">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="829f5-319">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="829f5-320">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="829f5-320">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="829f5-321">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="829f5-321">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="829f5-322">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="829f5-322">*Index.cshtml*</span></span>
  * <span data-ttu-id="829f5-323">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="829f5-323">*Index.cshtml.cs*</span></span>

<span data-ttu-id="829f5-324">*Debug* 구성에 프로젝트를 빌드하면 다음 *obj* 디렉터리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-324">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="829f5-325">**obj/**</span><span class="sxs-lookup"><span data-stu-id="829f5-325">**obj/**</span></span>
  * <span data-ttu-id="829f5-326">**Debug/**</span><span class="sxs-lookup"><span data-stu-id="829f5-326">**Debug/**</span></span>
    * <span data-ttu-id="829f5-327">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="829f5-327">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="829f5-328">**Razor/**</span><span class="sxs-lookup"><span data-stu-id="829f5-328">**Razor/**</span></span>
        * <span data-ttu-id="829f5-329">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="829f5-329">**Areas/**</span></span>
          * <span data-ttu-id="829f5-330">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="829f5-330">**Admin/**</span></span>
            * <span data-ttu-id="829f5-331">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="829f5-331">**Pages/**</span></span>
              * <span data-ttu-id="829f5-332">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="829f5-332">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="829f5-333">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="829f5-333">**Pages/**</span></span>
          * <span data-ttu-id="829f5-334">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="829f5-334">**Shared/**</span></span>
            * <span data-ttu-id="829f5-335">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="829f5-335">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="829f5-336">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="829f5-336">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="829f5-337">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="829f5-337">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="829f5-338">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="829f5-338">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="829f5-339">*pages/Index.cshtml*에 대해 생성된 클래스를 보려면 *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-339">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="829f5-340">ASP.NET Core MVC 프로젝트에 다음 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-340">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="829f5-341">`Startup.ConfigureServices`에서 MVC가 추가한 `RazorTemplateEngine`을 `CustomTemplateEngine` 클래스로 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-341">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="829f5-342">`CustomTemplateEngine`의 `return csharpDocument;` 문에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-342">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="829f5-343">중단점에서 프로그램 실행이 중지되면 `generatedCode`의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-343">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="829f5-345">보기 조회 및 대/소문자 구분</span><span class="sxs-lookup"><span data-stu-id="829f5-345">View lookups and case sensitivity</span></span>

<span data-ttu-id="829f5-346">Razor 보기 엔진은 보기에 대한 대/소문자 구분 조회를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-346">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="829f5-347">그러나 실제 조회는 기본 파일 시스템에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-347">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="829f5-348">파일 기반 원본:</span><span class="sxs-lookup"><span data-stu-id="829f5-348">File based source:</span></span>
  * <span data-ttu-id="829f5-349">파일 시스템이 대/소문자를 구문하지 않은 운영 체제(예: Windows)에서는 실제 파일 공급자 조회에서 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-349">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="829f5-350">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml*, */Views/home/test.cshtml* 및 기타 대/소문자 구분 변형과 일치하는 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-350">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="829f5-351">대/소문자를 구분하는 파일 시스템(예: Linux, OSX 및 `EmbeddedFileProvider`를 사용하는 파일 시스템)에서는 조회 시 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-351">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="829f5-352">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml*과 정확히 일치하는 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-352">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="829f5-353">미리 컴파일된 보기: ASP.NET 2.0 이상을 사용하는 경우 모든 운영 체제에서 미리 컴파일된 보기 조회 시 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-353">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="829f5-354">이 동작은 Windows의 물리적 파일 공급자 동작과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-354">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="829f5-355">미리 컴파일된 두 보기의 대소문자만 다른 경우 조회 결과가 불명확합니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-355">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="829f5-356">개발자는 파일 및 디렉터리 이름의 대/소문자를 다음의 대/소문자와 매칭하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-356">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="829f5-357">영역, 컨트롤러 및 작업 이름.</span><span class="sxs-lookup"><span data-stu-id="829f5-357">Area, controller, and action names.</span></span>
* <span data-ttu-id="829f5-358">Razor 페이지.</span><span class="sxs-lookup"><span data-stu-id="829f5-358">Razor Pages.</span></span>

<span data-ttu-id="829f5-359">대/소문자를 일치시키면 배포 시 기본 파일 시스템에 관계 없이 해당 보기를 잘 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="829f5-359">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
