---
title: ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션
author: ardalis
description: ASP.NET MVC 프로젝트를 ASP.NET Core MVC로 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652551"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션

작성자, [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)및 [Scott addie](https://scottaddie.com)

이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](../mvc/overview.md)로 마이그레이션하기 시작 하는 방법을 보여 줍니다. 이 프로세스에서는 ASP.NET MVC에서 변경 된 많은 사항을 강조 표시 합니다. ASP.NET MVC에서 마이그레이션은 다중 단계 프로세스 이며,이 문서에서는 초기 설정, 기본 컨트롤러 및 뷰, 정적 콘텐츠 및 클라이언트 쪽 종속성에 대해 설명 합니다. 추가 문서에서는 많은 ASP.NET MVC 프로젝트에 있는 구성 및 id 코드를 마이그레이션하는 방법을 다룹니다.

> [!NOTE]
> 샘플의 버전 번호는 최신 버전이 아닐 수 있습니다. 프로젝트를 적절 하 게 업데이트 해야 할 수도 있습니다.

## <a name="create-the-starter-aspnet-mvc-project"></a>스타터 ASP.NET MVC 프로젝트 만들기

업그레이드를 시연 하기 위해 먼저 ASP.NET MVC 앱을 만듭니다. *WebApp1* 이름으로 만듭니다. 그러면 네임 스페이스는 다음 단계에서 만드는 ASP.NET Core 프로젝트와 일치 합니다.

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택 된 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

*선택 사항:* 솔루션 이름을 *WebApp1* 에서 *Mvc5*로 변경 합니다. Visual Studio에서 새 솔루션 이름 (*Mvc5*)을 표시 합니다 .이를 통해 다음 프로젝트에서이 프로젝트를 더 쉽게 알 수 있습니다.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core 프로젝트 만들기

두 프로젝트의 네임 스페이스가 일치 하도록 이전 프로젝트와 동일한 이름을 사용 하 여 *비어* 있는 ASP.NET Core 웹 앱 (*WebApp1*)을 새로 만듭니다. 동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다. 동일한 이름을 사용 하려면 이전 프로젝트가 아닌 다른 디렉터리에이 프로젝트를 만들어야 합니다.

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![New ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 빈 프로젝트 템플릿을 선택 했습니다.](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *선택 사항:* *웹 응용 프로그램* 프로젝트 템플릿을 사용 하 여 새 ASP.NET Core 앱을 만듭니다. 프로젝트 이름을 *WebApp1*로 하 고 **개별 사용자 계정**에 대 한 인증 옵션을 선택 합니다. 이 앱의 이름을 *FullAspNetCore*로 바꿉니다. 이 프로젝트를 만들면 변환 시 시간이 절약 됩니다. 템플릿 생성 코드를 살펴보면 최종 결과를 보거나 변환 프로젝트에 코드를 복사할 수 있습니다. 템플릿 생성 프로젝트와 비교할 변환 단계에서 중단 되는 경우에도 유용 합니다.

## <a name="configure-the-site-to-use-mvc"></a>MVC를 사용 하도록 사이트 구성

::: moniker range=">= aspnetcore-2.1"

* .NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 가 기본적으로 참조 됩니다. 이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다. .NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* .NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage) 는 기본적으로 참조 됩니다. 이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다. .NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* .NET Core 또는 .NET Framework를 대상으로 지정 하는 경우 MVC 앱에서 자주 사용 하는 패키지는 프로젝트 파일에 개별적으로 나열 됩니다.

::: moniker-end

`Microsoft.AspNetCore.Mvc`은 ASP.NET Core MVC 프레임 워크입니다. `Microsoft.AspNetCore.StaticFiles`은 정적 파일 처리기입니다. ASP.NET Core 런타임은 모듈식 이며 정적 파일을 제공 하기 위해 명시적으로 옵트인 해야 합니다 ( [정적 파일](xref:fundamentals/static-files)참조).

* *Startup.cs* 파일을 열고 다음 코드와 일치 하도록 코드를 변경 합니다.

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

`UseStaticFiles` 확장 메서드는 정적 파일 처리기를 추가 합니다. 앞서 언급 했 듯이 ASP.NET 런타임은 모듈식 이며 정적 파일을 제공 하기 위해 명시적으로 옵트인 해야 합니다. `UseMvc` 확장 메서드는 라우팅을 추가 합니다. 자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup) 및 [라우팅](xref:fundamentals/routing)을 참조 하세요.

## <a name="add-a-controller-and-view"></a>컨트롤러 및 뷰 추가

이 섹션에서는 다음 섹션에서 마이그레이션할 ASP.NET MVC 컨트롤러 및 보기에 대 한 자리 표시자 역할을 하는 최소 컨트롤러 및 뷰를 추가 합니다.

* *Controllers* 폴더를 추가 합니다.

* *HomeController.cs* 라는 **컨트롤러 클래스** 를 *Controllers* 폴더에 추가 합니다.

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* *Views* 폴더를 추가 합니다.

* *보기/홈* 폴더를 추가 합니다.

* *Index. cshtml* 이라는 **Razor 뷰** 를 *Views/Home* 폴더에 추가 합니다.

![새 항목 추가 대화 상자](mvc/_static/view.png)

프로젝트 구조는 다음과 같습니다.

![WebApp1의 파일 및 폴더를 표시 하는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index. cshtml* 파일의 내용을 다음으로 바꿉니다.

```html
<h1>Hello world!</h1>
```

앱을 실행합니다.

![Microsoft Edge에서 웹 앱 열기](mvc/_static/hello-world.png)

자세한 내용은 [컨트롤러](xref:mvc/controllers/actions) 및 [뷰](xref:mvc/views/overview) 를 참조 하세요.

이제 최소한의 작업 ASP.NET Core 프로젝트가 있으므로 ASP.NET MVC 프로젝트에서 기능 마이그레이션을 시작할 수 있습니다. 다음을 이동 해야 합니다.

* 클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)

* controllers

* 뷰

* 모델

* 묶음

* filters

* 로그인/로그 아웃, Id (다음 자습서에서 수행 됨)

## <a name="controllers-and-views"></a>컨트롤러 및 뷰

* ASP.NET MVC `HomeController`의 각 메서드를 새 `HomeController`에 복사 합니다. ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은 [Actionresult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);입니다. ASP.NET Core MVC에서 작업 메서드는 대신 `IActionResult`을 반환 합니다. `ActionResult`에서 `IActionResult`를 구현 하므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.

* ASP.NET MVC 프로젝트에서 MVC ASP.NET Core 프로젝트에 *대 한* *파일을* *복사 합니다.*

* ASP.NET Core 앱을 실행 하 고 각 메서드를 테스트 합니다. 레이아웃 파일이 나 스타일을 아직 마이그레이션하지 않았으므로 렌더링 된 뷰에는 뷰 파일의 내용만 포함 됩니다. `About` 및 `Contact` 뷰에 대 한 레이아웃 파일 생성 링크가 없으므로 브라우저에서 호출 해야 합니다. **4492** 은 프로젝트에서 사용 되는 포트 번호로 바꿉니다.

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![연락처 페이지](mvc/_static/contact-page.png)

스타일 지정 및 메뉴 항목이 없다는 점에 유의 하십시오. 다음 섹션에서 이 문제를 해결합니다.

## <a name="static-content"></a>정적 콘텐츠

이전 버전의 ASP.NET MVC에서 정적 콘텐츠는 웹 프로젝트의 루트에서 호스팅되며 서버 쪽 파일과 혼합 되어 있습니다. ASP.NET Core에서 정적 콘텐츠는 *wwwroot* 폴더에 호스팅됩니다. 이전 ASP.NET MVC 앱의 정적 콘텐츠를 ASP.NET Core 프로젝트의 *wwwroot* 폴더로 복사 합니다. 이 샘플 변환에서:

* 이전 MVC 프로젝트의 *favicon* 파일을 ASP.NET Core 프로젝트의 *wwwroot* 폴더로 복사 합니다.

이전 ASP.NET MVC 프로젝트는 해당 스타일에 대해 [부트스트랩](https://getbootstrap.com/) 을 사용 하 고 *Content* 및 *Scripts* 폴더에 부트스트랩 파일을 저장 합니다. 이전 ASP.NET MVC 프로젝트를 생성 한 템플릿이 레이아웃 파일 (*Views/Shared/_Layout cshtml*)의 부트스트랩을 참조 합니다. ASP.NET MVC 프로젝트에서 새 프로젝트의 *wwwroot* 폴더로 *부트스트랩 .js* 및 *부트스트랩 .css* 파일을 복사할 수 있습니다. 대신, 다음 섹션에서 CDNs를 사용 하 여 부트스트랩 (및 기타 클라이언트 쪽 라이브러리)에 대 한 지원을 추가 합니다.

## <a name="migrate-the-layout-file"></a>레이아웃 파일 마이그레이션

* 이전 ASP.NET MVC 프로젝트의 *views* 폴더에 있는 *_ViewStart cshtml* 파일을 ASP.NET Core 프로젝트의 *views* 폴더에 복사 합니다. *_ViewStart cshtml* 파일은 ASP.NET Core MVC에서 변경 되지 않았습니다.

* *Views/Shared* 폴더를 만듭니다.

* *선택 사항:* *FullAspNetCore* MVC 프로젝트의 *views* 폴더에 있는 *_ViewImports* 를 ASP.NET Core 프로젝트의 *views* 폴더에 복사 합니다. *_ViewImports cshtml* 파일에서 네임 스페이스 선언을 제거 합니다. *_ViewImports cshtml* 파일은 모든 뷰 파일의 네임 스페이스를 제공 하 고 [태그 도우미](xref:mvc/views/tag-helpers/intro)에 가져옵니다. 태그 도우미는 새 레이아웃 파일에 사용 됩니다. *_ViewImports* 파일은 ASP.NET Core의 새로운 파일입니다.

* 이전 ASP.NET MVC 프로젝트의 *views/shared* 폴더에 있는 *_Layout cshtml* 파일을 ASP.NET Core 프로젝트의 *views/shared* 폴더에 복사 합니다.

*_Layout cshtml* 파일을 열고 다음과 같이 변경 합니다 (완성 된 코드는 아래에 표시 됨).

* `@Styles.Render("~/Content/css")`을 `<link>` 요소로 대체 하 여 *부트스트랩* 을 로드 합니다 (아래 참조).

* `@Scripts.Render("~/bundles/modernizr")`를 제거합니다.

* `@Html.Partial("_LoginPartial")` 줄을 주석으로 처리 합니다 (`@*...*@`로 줄 포함). 자세한 내용은 [ASP.NET Core 인증 및 Id 마이그레이션을](xref:migration/identity) 참조 하세요.

* `@Scripts.Render("~/bundles/jquery")`를 `<script>` 요소로 바꿉니다 (아래 참조).

* `@Scripts.Render("~/bundles/bootstrap")`를 `<script>` 요소로 바꿉니다 (아래 참조).

부트스트랩 CSS 포함에 대 한 대체 태그는 다음과 같습니다.

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery 및 부트스트랩 JavaScript 포함에 대 한 대체 태그는 다음과 같습니다.

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

브라우저에서 사이트를 봅니다. 이제 올바른 스타일을 적절히 로드 해야 합니다.

* *선택 사항:* 새 레이아웃 파일을 사용해 볼 수 있습니다. 이 프로젝트의 경우 *FullAspNetCore* 프로젝트에서 레이아웃 파일을 복사할 수 있습니다. 새 레이아웃 파일은 [태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용 하 고 다른 향상 된 기능을 포함 합니다.

## <a name="configure-bundling-and-minification"></a>번들링 및 축소 구성하기

묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](../client-side/bundling-and-minification.md)를 참조 하세요.

## <a name="solve-http-500-errors"></a>HTTP 500 오류 해결

문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다. 예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 발생 합니다. 기본적으로 ASP.NET Core apps에서는 `UseDeveloperExceptionPage` 확장이 `IApplicationBuilder`에 추가 되 고 구성이 *개발*될 때 실행 됩니다. 다음 코드에 자세히 설명 되어 있습니다.

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core 웹 앱에서 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다. 일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다. 자세한 내용은 [오류 처리](../fundamentals/error-handling.md) 에서 **개발자 예외 페이지 사용** 을 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
