---
title: ASP.NET Core의 Environment 태그 도우미
author: pkellner
description: 모든 속성을 포함하여 정의된 ASP.NET Core 환경 태그 도우미
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 308e7db47104ebd4d6bb8d08c64f14bbd118898b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653769"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core의 Environment 태그 도우미

작성자: [Peter Kellner](https://peterkellner.net) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

환경 태그 도우미는 현재 [호스팅 환경](xref:fundamentals/environments)에 기반하여 포함된 콘텐츠를 조건부로 렌더링합니다. 환경 태그 도우미의 단일 특성인 `names`는 쉼표로 구분된 환경 이름 목록입니다. 제공된 환경 이름 중 현재 환경과 일치하는 이름이 존재하면 포함된 콘텐츠가 렌더링됩니다.

태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

## <a name="environment-tag-helper-attributes"></a>Environment 태그 도우미의 특성

### <a name="names"></a>이름

`names`는 포함된 콘텐츠를 렌더링하도록 트리거하는 단일 호스팅 환경 이름 또는 쉼표로 구분된 호스팅 환경 이름의 목록을 지정할 수 있습니다.

환경 값은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)에서 반환한 현재 값과 비교됩니다. 이 비교에서 대/소문자는 무시됩니다.

다음 예에서는 환경 태그 도우미를 사용합니다. 호스팅 환경이 스테이징 또는 프로덕션일 경우 콘텐츠가 렌더링됩니다.

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>include 및 exclude 특성

`include` & `exclude` 특성은 포함되거나 제외될 호스팅 환경 이름을 기반으로 포함된 콘텐츠의 렌더링을 제어합니다.

### <a name="include"></a>include

`include` 속성은 `names` 특성과 유사한 동작을 수행합니다. `include` 특성 값에 나열된 환경은 앱의 호스팅 환경([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*))과 일치해야 `<environment>` 태그의 콘텐츠를 렌더링할 수 있습니다.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

`include` 특성과 달리, `<environment>` 태그의 콘텐츠는 호스팅 환경이 `exclude` 특성 값에 나열된 환경과 일치하지 않을 경우 렌더링됩니다.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/environments>
