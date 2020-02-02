---
title: Blazor 템플릿 ASP.NET Core
author: guardrex
description: ASP.NET Core Blazor 앱 템플릿 및 Blazor 프로젝트 구조에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885513"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>Blazor 템플릿 ASP.NET Core

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 프레임 워크는 각 Blazor 호스팅 모델에 대해 앱을 개발 하는 템플릿을 제공 합니다.

* Blazor Weasembmbse(`blazorwasm`)
* Blazor 서버 (`blazorserver`)

Blazor의 호스팅 모델에 대 한 자세한 내용은 <xref:blazor/hosting-models>을 참조 하세요.

템플릿에서 Blazor 앱을 만드는 방법에 대 한 단계별 지침은 <xref:blazor/get-started>를 참조 하세요.

## <a name="opno-locblazor-project-structure"></a>Blazor 프로젝트 구조

Blazor 템플릿에서 생성 된 Blazor 앱을 구성 하는 파일 및 폴더는 다음과 같습니다.

* *Program.cs* &ndash;를 설정 하는 앱의 진입점입니다.

  * ASP.NET Core [호스트](xref:fundamentals/host/generic-host) (Blazor 서버)
  * Weasembomomhost (Blazor Weasembomomomomomomoma) &ndash;이 파일의 코드는 Blazor weasembomtemplate (`blazorwasm`)에서 만든 앱에 대해 고유 합니다.
    * 앱의 루트 구성 요소인 `App` 구성 요소는 `Add` 메서드에 `app` DOM 요소로 지정 됩니다.
    * 호스트 작성기의 `ConfigureServices` 메서드 (예: `builder.Services.AddSingleton<IMyDependency, MyDependency>();`)를 사용 하 여 서비스를 구성할 수 있습니다.
    * 호스트 빌더 (`builder.Configuration`)를 통해 구성을 제공할 수 있습니다.

* *Startup.cs* (Blazor Server) &ndash;에는 앱의 시작 논리가 포함 됩니다. `Startup` 클래스는 두 가지 메서드를 정의 합니다.

  * `ConfigureServices` &ndash;는 응용 프로그램의 [DI (종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성 합니다. Blazor Server 앱에서 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>를 호출 하 여 서비스를 추가 하 고 `WeatherForecastService` 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 추가 합니다.
  * `Configure` &ndash;는 앱의 요청 처리 파이프라인을 구성 합니다.
    * 브라우저를 사용 하 여 실시간 연결에 대 한 끝점을 설정 하기 위해 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>가 호출 됩니다. 연결은 응용 프로그램에 실시간 웹 기능을 추가 하기 위한 프레임 워크인 [SignalR](xref:signalr/introduction)를 사용 하 여 만들어집니다.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 는 앱의 루트 페이지 (*Pages/_Host*)를 설정 하 고 탐색을 활성화 하기 위해 호출 됩니다.

* *wwwroot/index.html* (Blazor Weasembmbomembamboma)는 html 페이지로 구현 된 앱의 루트 페이지를 &ndash; 합니다.
  * 앱의 페이지를 처음 요청 하면이 페이지가 렌더링 되어 응답으로 반환 됩니다.
  * 페이지는 루트 `App` 구성 요소가 렌더링 되는 위치를 지정 합니다. `App` 구성 요소 (*응용 프로그램 razor*)는 `Startup.Configure`의 `AddComponent` 메서드에 `app` DOM 요소로 지정 됩니다.
  * `_framework/blazor.webassembly.js` JavaScript 파일이 로드 되며, 다음을 수행 합니다.
    * .NET 런타임, 앱 및 앱의 종속성을 다운로드 합니다.
    * 응용 프로그램을 실행 하도록 런타임을 초기화 합니다.

* *Pages/_Host* (Blazor Server)는 Razor 페이지로 구현 된 앱의 루트 페이지를 &ndash; 합니다.
  * 앱의 페이지를 처음 요청 하면이 페이지가 렌더링 되어 응답으로 반환 됩니다.
  * 브라우저와 서버 간에 실시간 SignalR 연결을 설정 하는 `_framework/blazor.server.js` JavaScript 파일이 로드 됩니다.
  * 호스트 페이지는 루트 `App` 구성 요소 (*응용 프로그램 razor*)가 렌더링 되는 위치를 지정 합니다.

* *응용 프로그램 razor* 는 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용 하 여 클라이언트 쪽 라우팅을 설정 하는 앱의 루트 구성 요소를 &ndash; 합니다. `Router` 구성 요소는 브라우저 탐색을 가로채서 요청 된 주소와 일치 하는 페이지를 렌더링 합니다.

* *페이지* 폴더 &ndash; Blazor 앱을 구성 하는 라우팅 가능 구성 요소/페이지 (*Razor*)를 포함 합니다. 각 페이지에 대 한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용 하 여 지정 됩니다. 템플릿에는 다음 구성 요소가 포함 되어 있습니다.
  * 홈 페이지를 구현 하는 `Index` (*인덱스 razor*) &ndash;입니다.
  * 카운터 페이지를 구현 하는 *`Counter` (&ndash;)입니다.*
  * `Error` (*오류. razor*, Blazor Server 앱에만 해당) 앱에서 처리 되지 않은 예외가 발생 하면 렌더링 &ndash;.
  * `FetchData` (*fetchdata. razor*) &ndash; Fetch data 페이지를 구현 합니다.

* *공유* 폴더 &ndash;는 앱에서 사용 하는 다른 UI 구성 요소 (razor)를 포함*합니다*.
  * `MainLayout` (*mainlayout. razor*)는 앱의 레이아웃 구성 요소 &ndash; 합니다.
  * `NavMenu` (*NavMenu*) &ndash;는 사이드바 탐색을 구현 합니다. 다른 Razor 구성 요소에 대 한 탐색 링크를 렌더링 하는 [NavLink 구성 요소](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함 합니다. 구성 요소가 로드 될 때 `NavLink` 구성 요소가 자동으로 선택 된 상태를 표시 하므로 사용자가 현재 표시 되는 구성 요소를 파악할 수 있습니다.

* *_Imports razor* &ndash;에는 네임 스페이스에 대 한 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 응용 프로그램의 구성 요소 (*Razor*)에 포함할 일반적인 razor 지시문이 포함 되어 있습니다.

* *데이터* 폴더 (Blazor 서버) &ndash;에는 응용 프로그램의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공 하는 `WeatherForecastService`의 `WeatherForecast` 클래스 및 구현이 포함 되어 있습니다.

* *wwwroot* &ndash; 앱의 공용 정적 자산이 포함 된 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.

* *appsettings* (Blazor Server) &ndash; 앱에 대 한 구성 설정입니다.
