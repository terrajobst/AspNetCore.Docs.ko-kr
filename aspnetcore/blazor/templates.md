---
title: ASP.NET Core Blazor 템플릿
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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649449"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Core Blazor 템플릿

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 프레임워크는 각 Blazor 호스팅 모델용 앱을 개발하기 위한 템플릿을 제공합니다.

* Blazor WebAssembly(`blazorwasm`)
* Blazor 서버(`blazorserver`)

Blazor의 호스팅 모델에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

템플릿에서 Blazor 앱을 만드는 방법에 대한 단계별 지침은 <xref:blazor/get-started>를 참조하세요.

## <a name="opno-locblazor-project-structure"></a>Blazor 프로젝트 구조

다음 파일 및 폴더는 Blazor 템플릿에서 생성된 Blazor 앱을 구성합니다.

* *Program.cs* &ndash; 설정된 앱의 진입점은 다음과 같습니다.

  * ASP.NET Core [호스트](xref:fundamentals/host/generic-host)(Blazor 서버)
  * WebAssembly 호스트(Blazor WebAssembly) &ndash; 이 파일의 코드는 Blazor WebAssembly 템플릿(`blazorwasm`)에서 만든 앱에 고유합니다.
    * 앱의 루트 구성 요소인 `App` 구성 요소는 `Add` 메서드에 대한 `app` DOM 요소로 지정됩니다.
    * 호스트 작성기(예: `builder.Services.AddSingleton<IMyDependency, MyDependency>();`)의 `ConfigureServices` 메서드로 서비스를 구성할 수 있습니다.
    * 호스트 작성기(`builder.Configuration`)를 통해 구성을 제공할 수 있습니다.

* *Startup.cs*(Blazor 서버) &ndash; 앱의 시작 논리를 포함합니다. `Startup` 클래스는 다음 두 가지 메서드를 정의합니다.

  * `ConfigureServices` &ndash; 앱의 DI([종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성합니다. Blazor 서버 앱에서 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>를 호출하여 서비스가 추가되고 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 `WeatherForecastService`가 추가됩니다.
  * `Configure` &ndash; 앱의 요청 처리 파이프라인을 구성합니다.
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>는 브라우저와의 실시간 연결을 위해 엔드포인트를 설정하도록 호출됩니다. 연결은 애플리케이션에 앱에 실시간 웹 기능을 추가하기 위한 프레임워크인 [SignalR](xref:signalr/introduction)를 사용하여 만들어집니다.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)를 호출하여 앱의 루트 페이지(*Pages/_Host*)를 설정하고 탐색을 사용하도록 설정합니다.

* *wwwroot/index.html*(Blazor WebAssembly) &ndash; HTML 페이지로 구현된 앱의 루트 페이지입니다.
  * 앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.
  * 이 페이지는 루트 `App` 구성 요소가 렌더링되는 위치를 지정합니다. `App` 구성 요소(*App.razor*)는 `Startup.Configure`에서 `AddComponent` 메서드에 대한 `app` DOM 요소로 지정됩니다.
  * `_framework/blazor.webassembly.js` JavaScript 파일이 로드되며, 다음을 수행합니다.
    * .NET 런타임, 앱 및 앱의 종속성을 다운로드합니다.
    * 런타임을 초기화하여 앱을 실행합니다.

* *Pages/_Host cshtml*(Blazor 서버) &ndash; Razor 페이지로 구현된 앱의 루트 페이지입니다.
  * 앱의 페이지를 처음 요청되면 이 페이지가 렌더링되어 응답으로 반환됩니다.
  * 브라우저와 서버 간에 실시간 SignalR 연결을 설정하는 `_framework/blazor.server.js` JavaScript 파일이 로드됩니다.
  * 호스트 페이지는 루트 `App` 구성 요소(*App.razor*)가 렌더링되는 위치를 지정합니다.

* *App.razor* &ndash; <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하여 클라이언트 쪽 라우팅을 설정하는 앱의 루트 구성 요소입니다. `Router` 구성 요소는 브라우저 탐색을 가로채서 요청된 주소와 일치하는 페이지를 렌더링합니다.

* *Pages* 폴더 &ndash; Blazor 앱을 구성하는 라우팅 가능한 구성 요소/페이지( *.razor*)가 포함되어 있습니다. 각 페이지에 대한 경로는 [`@page`](xref:mvc/views/razor#page) 지시어를 사용하여 지정합니다. 이 템플릿은 다음 구성 요소를 포함합니다.
  * `Index`(*Index. razor*)&ndash; 홈페이지를 구현합니다.
  * `Counter`(*Counter.razor*)&ndash; 카운터 페이지를 구현합니다.
  * `Error`(*Error.razor*, Blazor 서버 앱만 해당) &ndash; 앱에서 처리되지 않은 예외가 발생하면 렌더링됩니다.
  * `FetchData`(*FetchData.razor*)&ndash; 데이터 가져오기 페이지를 구현합니다.

* *Shared* 폴더 &ndash; 앱에서 사용되는 다른 UI 구성 요소( *.razor*)가 포함되어 있습니다.
  * `MainLayout`(*MainLayout.razor*)&ndash; 앱의 레이아웃 구성 요소입니다.
  * `NavMenu`(*NavMenu.razor*)&ndash; 사이드바 탐색을 구현합니다. 다른 Razor 구성 요소에 대한 탐색 링크를 렌더링하는 [NavLink 구성 요소](xref:blazor/routing#navlink-component)(<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)가 포함되어 있습니다. `NavLink` 구성 요소는 해당 구성 요소가 로드될 때 선택된 상태를 자동으로 나타내므로 사용자가 현재 표시되는 구성 요소를 파악하는 데 도움이 됩니다.

* *_Imports razor* &ndash; 네임스페이스에 대한 [`@using`](xref:mvc/views/razor#using) 지시문과 같은 앱의 구성 요소( *.razor*)에 포함할 일반적인 Razor 지시문이 포함되어 있습니다.

* *Data* 폴더(Blazor 서버) &ndash; 앱의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공하는 `WeatherForecast` 클래스 및 `WeatherForecastService` 구현이 포함되어 있습니다.

* *wwwroot* &ndash; 앱의 퍼블릭 정적 자산을 포함하는 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.

* *appsettings.json*(Blazor 서버) &ndash; 앱에 대한 구성 설정입니다.
