---
title: ASP.NET Core의 Script 태그 도우미
author: rick-anderson
ms.author: riande
description: ASP.NET Core Script 태그 도우미 특성 및 HTML 스크립트 태그의 동작을 확장할 때 각 특성이 담당하는 역할을 확인합니다.
ms.custom: mvc
ms.date: 12/02/2019
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 8a90eb5a74ff3f8178a47c59ad7ba1b6a389ab87
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717379"
---
# <a name="script-tag-helper-in-aspnet-core"></a>ASP.NET Core의 Script 태그 도우미

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Script 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)는 기본 또는 대체(fallback) 스크립트 파일에 대한 링크를 생성합니다. 일반적으로 기본 스크립트 파일은 [콘텐츠 배달 네트워크](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn)(CDN)에 존재합니다.

[!INCLUDE[](~/includes/cdn.md)]

Script 태그 도우미를 사용하면 스크립트 파일에 대한 CDN과 CDN을 사용할 수 없는 경우에 대비한 대체 항목을 지정할 수 있습니다. Script 태그 도우미는 로컬 호스팅의 견고성과 함께 CDN의 성능 이점을 제공합니다.

다음 Razor 태그는 대체가 포함된 `script` 요소를 보여 줍니다.

```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

CDN 스크립트 로드를 지연시키기 위해 `<script>` 요소의 [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) 특성을 사용하지 마세요. Script 태그 도우미는 [asp-fallback-test](#asp-fallback-test) 식을 즉시 실행하는 JavaScript를 렌더링합니다. CDN 스크립트 로딩이 지연되면 식이 실패합니다.

## <a name="commonly-used-script-tag-helper-attributes"></a>일반적으로 사용되는 Script 태그 도우미 특성

모든 Script 태그 도우미 특성, 속성 및 메서드는 [Script 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)를 참조하세요.

### <a name="asp-fallback-test"></a>asp-fallback-test

대체 테스트에 사용할 기본 스크립트에 정의된 스크립트 메서드입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>을 참조하세요.

### <a name="asp-fallback-src"></a>asp-fallback-src

기본 항목에서 오류가 발생하는 경우 대체할 스크립트 태그의 URL입니다. 자세한 내용은 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
