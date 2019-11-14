---
title: ASP.NET Core의 브라우저 링크
author: ncarandini
description: 브라우저 링크가 하나 이상의 웹 브라우저와 개발 환경을 연결 하는 Visual Studio 기능을 설명 합니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962792"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core의 브라우저 링크

[Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)및 [Tom Dykstra](https://github.com/tdykstra)

브라우저 링크는 Visual Studio에서 개발 환경과 하나 이상의 웹 브라우저 간에 통신 채널을 만드는 기능입니다. 브라우저 링크를 사용 하 여 여러 브라우저에서 웹 응용 프로그램을 한 번에 새로 고칠 수 있으며,이는 브라우저 간 테스트에 유용 합니다.

## <a name="browser-link-setup"></a>브라우저 링크 설정

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.0 프로젝트를 ASP.NET Core 2.1로 변환 하 고 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)로 전환 하는 경우 브라우저 링크 기능을 위한 [VisualStudio 링크](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지를 설치 합니다. ASP.NET Core 2.1 프로젝트 템플릿은 기본적으로 `Microsoft.AspNetCore.App` 메타 패키지를 사용 합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **웹 응용 프로그램**, **빈**및 **Web API** 프로젝트 템플릿은 메타 패키지 링크를 포함 하는 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)에 대 한 패키지 참조를 포함 하는 [AspNetCore](xref:fundamentals/metapackage)를 사용 합니다. 따라서 `Microsoft.AspNetCore.All` 메타 패키지를 사용 하려면 브라우저 링크를 사용할 수 있도록 하는 추가 작업이 필요 하지 않습니다.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **웹 응용 프로그램** 프로젝트 템플릿에는 [VisualStudio 링크](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 패키지에 대 한 패키지 참조가 있습니다. **빈** 또는 **Web API** 템플릿 프로젝트를 `Microsoft.VisualStudio.Web.BrowserLink`에 대 한 패키지 참조를 추가 해야 합니다.

Visual Studio 기능 이므로 **빈** 또는 **Web API** 템플릿 프로젝트에 패키지를 추가 하는 가장 쉬운 방법은 **패키지 관리자 콘솔** 을 열고 ( **다른 Windows** > **패키지 관리자 콘솔**>**보기** ) 다음 명령을 실행 하는 것입니다.

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

또는 **NuGet 패키지 관리자**를 사용할 수 있습니다. **솔루션 탐색기** 에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭 하 고 **NuGet 패키지 관리**를 선택 합니다.

![NuGet 패키지 관리자 열기](using-browserlink/_static/open-nuget-package-manager.png)

패키지를 찾아서 설치 합니다.

![NuGet 패키지 관리자를 사용 하 여 패키지 추가](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>Configuration

`Startup.Configure` 메서드에서:

```csharp
app.UseBrowserLink();
```

일반적으로 코드는 다음과 같이 개발 환경에서 브라우저 링크만 사용 하도록 설정 하는 `if` 블록 내에 있습니다.

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

자세한 내용은 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.

## <a name="how-to-use-browser-link"></a>브라우저 링크를 사용 하는 방법

ASP.NET Core 프로젝트를 열면 Visual Studio에서 **디버그 대상** 도구 모음 컨트롤 옆에 브라우저 링크 도구 모음 컨트롤을 표시 합니다.

![브라우저 링크 드롭다운 메뉴](using-browserlink/_static/browserLink-dropdown-menu.png)

브라우저 링크 도구 모음 컨트롤에서 다음을 수행할 수 있습니다.

* 한 번에 여러 브라우저에서 웹 응용 프로그램을 새로 고칩니다.
* **브라우저 링크 대시보드**를 엽니다.
* **브라우저 링크**를 사용 하거나 사용 하지 않도록 설정 합니다. 참고: 브라우저 링크는 Visual Studio 2017 (15.3)에서 기본적으로 사용 하지 않도록 설정 되어 있습니다.
* [CSS 자동 동기화](#enable-or-disable-css-auto-sync)를 사용 하거나 사용 하지 않도록 설정 합니다.

> [!NOTE]
> 일부 Visual Studio 플러그 인, 특히 *웹 확장 팩 2015* 및 *웹 확장 팩 2017*은 브라우저 링크에 대 한 확장 기능을 제공 하지만 일부 추가 기능은 ASP.NET Core 프로젝트에서 작동 하지 않습니다.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>한 번에 여러 브라우저에서 웹 앱을 새로 고칩니다.

프로젝트를 시작할 때 실행할 단일 웹 브라우저를 선택 하려면 **디버그 대상** 도구 모음 컨트롤의 드롭다운 메뉴를 사용 합니다.

![F5 드롭다운 메뉴](using-browserlink/_static/debug-target-dropdown-menu.png)

한 번에 여러 브라우저를 열려면 동일한 드롭다운에서 **찾아보기** ...를 선택 합니다. CTRL 키를 누른 채 원하는 브라우저를 선택 하 고 **찾아보기**를 클릭 합니다.

![한 번에 많은 브라우저 열기](using-browserlink/_static/open-many-browsers-at-once.png)

인덱스 뷰가 열려 있는 Visual Studio와 두 개의 열린 브라우저가 표시 되는 스크린샷은 다음과 같습니다.

![두 브라우저의 동기화 예제](using-browserlink/_static/sync-with-two-browsers-example.png)

브라우저 링크 도구 모음 컨트롤을 마우스로 가리켜 프로젝트에 연결 된 브라우저를 확인 합니다.

![가리키기 팁](using-browserlink/_static/hoover-tip.png)

브라우저 링크 새로 고침 단추를 클릭 하면 인덱스 뷰를 변경 하 고 연결 된 모든 브라우저를 업데이트 합니다.

![브라우저-변경 내용 동기화](using-browserlink/_static/browsers-sync-to-changes.png)

브라우저 링크는 Visual Studio 외부에서 시작 하 여 응용 프로그램 URL로 이동 하는 브라우저 에서도 작동 합니다.

### <a name="the-browser-link-dashboard"></a>브라우저 링크 대시보드

브라우저 링크 드롭다운 메뉴에서 브라우저 링크 대시보드를 열어 오픈 브라우저와의 연결을 관리 합니다.

![browserslink-대시보드](using-browserlink/_static/open-browserlink-dashboard.png)

브라우저가 연결 되지 않은 경우 *브라우저에서 보기* 링크를 선택 하 여 디버깅 하지 않는 세션을 시작할 수 있습니다.

![브라우저 링크-대시보드-연결 없음](using-browserlink/_static/browserlink-dashboard-no-connections.png)

그렇지 않으면 연결 된 브라우저가 각 브라우저가 표시 하는 페이지에 대 한 경로와 함께 표시 됩니다.

![브라우저 링크-대시보드-2-연결](using-browserlink/_static/browserlink-dashboard-two-connections.png)

원하는 경우 나열 된 브라우저 이름을 클릭 하 여 단일 브라우저를 새로 고칠 수 있습니다.

### <a name="enable-or-disable-browser-link"></a>브라우저 링크 사용 또는 사용 안 함

브라우저 링크를 사용 하지 않도록 설정한 후 다시 사용 하도록 설정 하는 경우 브라우저를 새로 고쳐 다시 연결 해야 합니다.

### <a name="enable-or-disable-css-auto-sync"></a>CSS 자동 동기화 사용 또는 사용 안 함

CSS 자동 동기화를 사용 하도록 설정 하면 CSS 파일을 변경할 때 연결 된 브라우저가 자동으로 새로 고쳐집니다.

## <a name="how-it-works"></a>작동 방식

브라우저 링크 SignalR를 사용 하 여 Visual Studio와 브라우저 간에 통신 채널을 만듭니다. 브라우저 링크를 사용 하는 경우 Visual Studio는 여러 클라이언트 (브라우저)가 연결할 수 있는 SignalR 서버 역할을 합니다. 또한 브라우저 링크는 ASP.NET Core 요청 파이프라인에 미들웨어 구성 요소를 등록 합니다. 이 구성 요소는 서버의 모든 페이지 요청에 대 한 특수 `<script>` 참조를 삽입 합니다. 브라우저에서 **소스 보기** 를 선택 하 고 `<body>` 태그 내용 끝으로 스크롤하면 스크립트 참조를 볼 수 있습니다.

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

소스 파일은 수정 되지 않습니다. 미들웨어 구성 요소는 스크립트 참조를 동적으로 삽입 합니다.

브라우저 쪽 코드는 모두 JavaScript 이므로 브라우저 플러그 인을 요구 하지 않고 SignalR 지 원하는 모든 브라우저에서 작동 합니다.
