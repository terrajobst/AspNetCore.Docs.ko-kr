---
title: ASP.NET Core에서 브라우저 링크
author: ncarandini
description: 브라우저 링크는 하나 이상의 웹 브라우저를 사용 하 여 개발 환경에 연결 하는 Visual Studio 기능 하는 방법을 설명 합니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894710"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core에서 브라우저 링크

하 여 [Nicolò Carandini](https://github.com/ncarandini)하십시오 [Mike Wasson](https://github.com/MikeWasson), 및 [Tom Dykstra](https://github.com/tdykstra)

브라우저 링크에는 Visual studio 개발 환경 및 하나 이상의 웹 브라우저 간에 통신 채널을 만드는 기능입니다. 브라우저 간 테스트에 유용한 여러 브라우저에서 웹 응용 프로그램에 한 번만 새로 고치려면 브라우저 링크를 사용할 수 있습니다.

## <a name="browser-link-setup"></a>브라우저 링크 설치

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.1 및 전환 하는 ASP.NET Core 2.0 프로젝트를 변환 하는 경우는 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app)를 설치 합니다 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지 BrowserLink 기능입니다. ASP.NET Core 2.1 프로젝트 템플릿을 사용 하 여는 `Microsoft.AspNetCore.App` 기본적으로 메타 패키지입니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **웹 응용 프로그램**, **빈**, 및 **Web API** 프로젝트 템플릿을 사용 합니다 [Microsoft.AspNetCore.All 메타 패키지](xref:fundamentals/metapackage) 에 대 한 패키지 참조를 포함 하는 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)합니다. 따라서를 사용 하 여 `Microsoft.AspNetCore.All` 메타 패키지는 브라우저 링크를 사용 하기 위해 사용할 수 있도록 하려면 추가 작업이 없으므로 필요 합니다.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **웹 응용 프로그램** 프로젝트 서식 파일에 대 한 패키지 참조에는 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지 있습니다. **빈** 하거나 **Web API** 서식 파일 프로젝트에 대 한 패키지 참조를 추가 해야 `Microsoft.VisualStudio.Web.BrowserLink`합니다.

이므로 Visual Studio 기능에는 가장 쉬운 방법은 패키지를 추가 하는 **빈** 또는 **Web API** 템플릿 프로젝트를 여는 것은 **패키지 관리자 콘솔** (**뷰** > **기타 Windows** > **패키지 관리자 콘솔**) 다음 명령을 실행 합니다.

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

사용할 수 있습니다 **NuGet 패키지 관리자**합니다. 프로젝트 이름을 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택한 **NuGet 패키지 관리**:

![열고 NuGet 패키지 관리자](using-browserlink/_static/open-nuget-package-manager.png)

찾기 및 패키지를 설치 합니다.

![NuGet 패키지 관리자를 사용 하 여 패키지를 추가 합니다.](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>구성

`Startup.Configure` 메서드에서:

```csharp
app.UseBrowserLink();
```

내 코드는 일반적으로 `if` 다음과 같이 개발 환경에서 브라우저 링크 수만 있는 블록:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)를 참조하세요.

## <a name="how-to-use-browser-link"></a>브라우저 링크를 사용 하는 방법

Visual Studio 브라우저 링크 도구 모음 컨트롤 옆에 표시는 ASP.NET Core 프로젝트가 열려 있으면 합니다 **디버그 대상** toolbar 컨트롤:

![브라우저 링크 드롭 다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.

* 여러 브라우저에서 웹 응용 프로그램을 한 번 새로 고칩니다.
* 엽니다는 **브라우저 링크 대시보드**합니다.
* 또는 사용 안 함 **브라우저 링크**합니다. 참고: 브라우저 링크는 Visual Studio 2017 (15.3)에서 기본적으로 비활성화 됩니다.
* 또는 사용 안 함 [CSS 자동 동기화](#enable-or-disable-css-auto-sync)합니다.

> [!NOTE]
> 일부 Visual Studio 플러그 인, 가장 주목할 만한 *웹 확장 팩 2015* 하 고 *웹 확장 팩 2017*, 브라우저 링크에 대 한 확장 된 기능을 제공 하지만 추가 기능 중 일부는 ASP를 사용 하 여 작동 하지 않습니다. .NET Core 프로젝트입니다.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>한 번에 여러 브라우저에서 웹 앱을 새로 고침

프로젝트를 시작할 때 시작 하는 단일 웹 브라우저를 선택 하려면 드롭다운 메뉴에서를 사용 합니다 **디버그 대상** toolbar 컨트롤:

![F5 드롭 다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

한 번에 여러 브라우저를 열고, 선택 **찾아보기...**  동일한 드롭다운 목록에서. 원하는 브라우저를 선택 하려면 CTRL 키를 누른 채 클릭 **찾아보기**:

![한 번에 다양 한 브라우저를 열으십시오](using-browserlink/_static/open-many-browsers-at-once.png)

인덱스 뷰를 사용 하 여 Visual Studio를 열고 보여 주는 스크린샷 및 열린 브라우저는 다음과 같습니다.

![두 개의 브라우저 예제를 사용 하 여 동기화](using-browserlink/_static/sync-with-two-browsers-example.png)

프로젝트에 연결 된 브라우저를 확인 하려면 브라우저 링크 도구 모음 컨트롤을 마우스로:

![Hover 팁](using-browserlink/_static/hoover-tip.png)

인덱스 뷰를 변경 하 고 모든 연결 된 브라우저는 브라우저 링크 새로 고침 단추를 클릭할 때 업데이트 됩니다.

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

브라우저 링크 Visual Studio 외부에서 시작 하 고 응용 프로그램 URL로 이동 하는 브라우저 에서도 작동 합니다.

### <a name="the-browser-link-dashboard"></a>브라우저 링크 대시보드

브라우저 링크 드롭다운 메뉴를 열고 브라우저를 사용 하 여 연결을 관리 목록에서 브라우저 링크 대시보드를 엽니다.

![오픈-browserslink-대시보드](using-browserlink/_static/open-browserlink-dashboard.png)

선택 하 여 비 디버깅 세션을 시작할 수 없는 브라우저에 연결 하는 경우는 *브라우저에서 보기* 링크:

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

그렇지 않으면 연결 된 브라우저는 각 브라우저에 표시 되는 페이지의 경로를 사용 하 여 표시 됩니다.

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

원하는 경우 해당 단일 브라우저를 새로 고치려면 나열 된 브라우저 이름을 클릭할 수 있습니다.

### <a name="enable-or-disable-browser-link"></a>브라우저 링크를 사용할지 설정 합니다.

이 해제 한 후 브라우저 링크를 다시 설정 하면 해당 다시 연결 하려면 브라우저를 새로 고쳐야 합니다.

### <a name="enable-or-disable-css-auto-sync"></a>CSS 자동 동기화를 사용할지 설정 합니다.

CSS 자동 동기화를 사용 하는 경우 연결 된 브라우저는 자동으로 새로 고쳐집니다 CSS 파일을 변경 하는 경우.

## <a name="how-it-works"></a>작동 방법

브라우저 링크 SignalR을 사용 하 여 Visual Studio와 브라우저 간에 통신 채널을 만듭니다. 브라우저 링크를 사용 하는 경우 Visual Studio는 여러 클라이언트 (브라우저)에 연결할 수 있는 SignalR 서버로 작동 합니다. 브라우저 링크는 또한 ASP.NET Core 요청 파이프라인에 미들웨어 구성 요소를 등록합니다. 이 구성 요소를 특수 삽입 `<script>` 서버에서 모든 페이지 요청에 대 한 참조입니다. 선택 하 여 스크립트 파일에 대 한 참조를 확인할 수 있습니다 **소스 보기** 브라우저 및 스크롤의 끝에는 `<body>` 콘텐츠 태그:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

소스 파일을 수정 되지 않습니다. 미들웨어 구성 요소 스크립트 참조를 동적으로 삽입합니다.

브라우저 쪽 코드를 모든 JavaScript 이기 때문에 브라우저 플러그 인을 요구 하지 않고 SignalR을 지 원하는 모든 브라우저에서 작동 합니다.
