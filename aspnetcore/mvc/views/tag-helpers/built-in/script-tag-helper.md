---
title: ASP.NET Core의 Script 태그 도우미
author: rick-anderson
ms.author: riande
description: ASP.NET Core Script 태그 도우미 특성 및 HTML 스크립트 태그의 동작을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256467"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="80219-103">ASP.NET Core의 Script 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="80219-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="80219-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="80219-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="80219-105">[Script 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)는 기본 또는 대체(fallback) 스크립트 파일에 대한 링크를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="80219-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="80219-106">일반적으로 기본 스크립트 파일은 [콘텐츠 배달 네트워크](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn)(CDN)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80219-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="80219-107">Script 태그 도우미를 사용하면 스크립트 파일에 CDN을 지정하고 CDN을 사용할 수 없는 경우 대체(fallback) 항목을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80219-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="80219-108">Script 태그 도우미는 로컬 호스팅의 견고성을 사용하여 CDN의 성능 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="80219-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="80219-109">다음 Razor 표시는 ASP.NET Core 웹앱 템플릿을 사용하여 만든 레이아웃 파일의 `script` 요소를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="80219-109">The following Razor markup shows the `script` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

<span data-ttu-id="80219-110">다음은 위의 코드에서 렌더링된 HTML과 유사합니다(비개발 환경에서).</span><span class="sxs-lookup"><span data-stu-id="80219-110">The following is similar to the rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

<span data-ttu-id="80219-111">위의 코드에서 Script 태그 도우미는 `window.jQuery`에 대해 테스트하는 두 번째 스크립트(`<script>  (window.jQuery || document.write(`) 요소를 생성했습니다.</span><span class="sxs-lookup"><span data-stu-id="80219-111">In the preceding code, the Script Tag Helper generated the second script ( `<script>  (window.jQuery || document.write(`) element, which tests for `window.jQuery`.</span></span> <span data-ttu-id="80219-112">`window.jQuery`를 찾을 수 없는 경우 `document.write(`가 실행되어 스크립트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="80219-112">If `window.jQuery` is not found, `document.write(` runs and creates a script</span></span> 

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="80219-113">일반적으로 사용되는 Script 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="80219-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="80219-114">모든 Script 태그 도우미 특성, 속성 및 메서드는 [Script 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80219-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="80219-115">href</span><span class="sxs-lookup"><span data-stu-id="80219-115">href</span></span>

<span data-ttu-id="80219-116">링크된 리소스의 기본 설정 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="80219-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="80219-117">주소는 모든 경우에 생성된 HTML로 간주되어 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="80219-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="80219-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="80219-118">asp-fallback-href</span></span>

<span data-ttu-id="80219-119">기본 URL에 오류가 발생할 경우 대체(fallback)할 CSS 스타일시트의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="80219-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="80219-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="80219-120">asp-fallback-test-class</span></span>

<span data-ttu-id="80219-121">대체(fallback) 테스트에 사용할 스타일시트에 정의된 클래스 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="80219-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="80219-122">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80219-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="80219-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="80219-123">asp-fallback-test-property</span></span>

<span data-ttu-id="80219-124">대체(fallback) 테스트에 사용할 CSS 속성 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="80219-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="80219-125">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80219-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="80219-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="80219-126">asp-fallback-test-value</span></span>

<span data-ttu-id="80219-127">대체(fallback) 테스트에 사용할 CSS 속성 값입니다.</span><span class="sxs-lookup"><span data-stu-id="80219-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="80219-128">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80219-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="80219-129">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="80219-129">asp-fallback-test-value</span></span>

<span data-ttu-id="80219-130">대체(fallback) 테스트에 사용할 CSS 속성 값입니다.</span><span class="sxs-lookup"><span data-stu-id="80219-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="80219-131">자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80219-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="80219-132">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="80219-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
