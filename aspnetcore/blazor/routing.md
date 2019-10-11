---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법 및 NavLink 구성 요소에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2019
uid: blazor/routing
ms.openlocfilehash: 8f48112237e6dd3fed88404c53b8d7d9137ef6ff
ms.sourcegitcommit: 0b8a7571bf7acf85bf16118acb2435001cbe4b5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72236535"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 라우팅

[Luke Latham](https://github.com/guardrex)으로

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor apps에서 요청을 라우팅하는 방법 및 `NavLink` 구성 요소를 사용 하 여 탐색 링크를 만드는 방법에 대해 알아봅니다.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 끝점 라우팅 통합

Blazor 서버는 [ASP.NET Core 끝점 라우팅](xref:fundamentals/routing)에 통합 됩니다. ASP.NET Core 앱은 `Startup.Configure`에서 `MapBlazorHub` 인 대화형 구성 요소에 대 한 들어오는 연결을 허용 하도록 구성 됩니다.

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

가장 일반적인 구성은 모든 요청을 Blazor Server 앱의 서버 쪽에 대 한 호스트 역할을 하는 Razor 페이지로 라우팅하는 것입니다. 규칙에 따라 *호스트* 페이지의 이름은 일반적으로 *_Host*입니다. 호스트 파일에 지정 된 경로는 경로 일치에서 낮은 우선 순위로 작동 하므로 *대체 경로* 라고 합니다. 다른 경로가 일치 하지 않는 경우 대체 (fallback) 경로를 고려 합니다. 이렇게 하면 앱이 Blazor 서버 앱을 방해 하지 않고 다른 컨트롤러 및 페이지를 사용할 수 있습니다.

## <a name="route-templates"></a>경로 템플릿

@No__t-0 구성 요소는 지정 된 경로를 사용 하 여 각 구성 요소로 라우팅할 수 있도록 합니다. @No__t-0 구성 요소가 *응용 프로그램 razor* 파일에 표시 됩니다.

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

@No__t-1 지시문이 있는 *razor* 파일이 컴파일되면 생성 된 클래스에 경로 템플릿을 지정 하는 @no__t 2가 제공 됩니다.

런타임에 `RouteView` 구성 요소는 다음과 같습니다.

* @No__t-1에서 원하는 모든 매개 변수와 함께 `RouteData`을 받습니다.
* 지정 된 매개 변수를 사용 하 여 지정 된 구성 요소를 해당 레이아웃 또는 선택적 기본 레이아웃으로 렌더링 합니다.

레이아웃을 지정 하지 않는 구성 요소에 사용할 레이아웃 클래스가 포함 된 `DefaultLayout` 매개 변수를 선택적으로 지정할 수 있습니다. 기본 Blazor 템플릿은 `MainLayout` 구성 요소를 지정 합니다. *Mainlayout. razor* 는 템플릿 프로젝트의 *공유* 폴더에 있습니다. 레이아웃에 대 한 자세한 내용은 <xref:blazor/layouts>을 참조 하세요.

여러 경로 템플릿을 구성 요소에 적용할 수 있습니다. 다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대 한 요청에 응답 합니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Url이 올바르게 확인 될 수 있도록 앱은 *wwwroot/index.html* 파일 (Blazor Weasembome) 또는 *Pages/_Host* 파일 (Blazor Server @no__t)에 `<base>` 태그를 포함 해야 합니다 (`<base href="/">`). 자세한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>을 참조하세요.

## <a name="provide-custom-content-when-content-isnt-found"></a>콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공

@No__t-0 구성 요소를 사용 하면 요청 된 경로에 대 한 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.

*응용 프로그램 razor* 파일에서 `Router` 구성 요소의 `NotFound` 템플릿 매개 변수에서 사용자 지정 콘텐츠를 설정 합니다.

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

@No__t-0 태그의 내용에는 다른 대화형 구성 요소와 같은 임의의 항목이 포함 될 수 있습니다. @No__t-0 내용에 기본 레이아웃을 적용 하려면 <xref:blazor/layouts>을 참조 하세요.

## <a name="route-to-components-from-multiple-assemblies"></a>여러 어셈블리에서 구성 요소로 라우팅

@No__t-0 매개 변수를 사용 하 여 라우팅할 수 있는 구성 요소를 검색할 때 고려할 `Router` 구성 요소에 대 한 추가 어셈블리를 지정 합니다. 지정 된 어셈블리는 @no__t 64, 어셈블리에 추가 된 것으로 간주 됩니다. 다음 예제에서 `Component1`은 참조 된 클래스 라이브러리에 정의 된 라우팅할 수 있는 구성 요소입니다. 다음 `AdditionalAssemblies` 예제에서는 `Component1`에 대 한 라우팅을 지원 합니다.

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }>
    ...
</Router>
```

## <a name="route-parameters"></a>경로 매개 변수

라우터는 경로 매개 변수를 사용 하 여 해당 구성 요소 매개 변수를 동일한 이름 (대/소문자 구분 안 함)으로 채웁니다.

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

선택적 매개 변수는 ASP.NET Core 3.0에서 Blazor apps에 대해 지원 되지 않습니다. 이전 예제에서는 두 개의 `@page` 지시문이 적용 됩니다. 첫 번째는 매개 변수 없이 구성 요소에 대 한 탐색을 허용 합니다. 두 번째 `@page` 지시어는 `{text}` 경로 매개 변수를 사용 하 고 값을 `Text` 속성에 할당 합니다.

## <a name="route-constraints"></a>경로 제약 조건

경로 제약 조건은 경로 세그먼트에 대 한 형식 일치를 구성 요소에 적용 합니다.

다음 예제에서 `Users` 구성 요소에 대 한 경로는 다음과 같은 경우에만 일치 합니다.

* @No__t-0 경로 세그먼트가 요청 URL에 있습니다.
* @No__t-0 세그먼트는 정수 (`int`)입니다.

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

다음 표에 표시 된 경로 제약 조건을 사용할 수 있습니다. 고정 문화권과 일치 하는 경로 제약 조건에 대 한 자세한 내용은 테이블 아래 경고를 참조 하세요.

| 제약 조건 | 예제           | 일치하는 예제                                                                  | 고정<br>Culture<br>일치 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | 아니요                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | 예                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | 예                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | 예                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | 예                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 아니요                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | 예                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | 예                              |

> [!WARNING]
> CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다. 이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.

### <a name="routing-with-urls-that-contain-dots"></a>점이 포함 된 Url로 라우팅

Blazor Server 앱에서 *_Host* 의 기본 경로는 `/` (`@page "/"`)입니다. URL이 파일을 요청 하는 것으로 나타나기 때문에 점 (`.`)을 포함 하는 요청 URL이 기본 경로와 일치 하지 않습니다. Blazor 앱은 존재 하지 않는 정적 파일에 대해 *404-찾을 수* 없음 응답을 반환 합니다. 점이 포함 된 경로를 사용 하려면 다음 경로 템플릿을 사용 하 여 _Host를 구성 *합니다* .

```cshtml
@page "/{**path}"
```

@No__t-0 템플릿은 다음을 포함 합니다.

* *이중 @no__t 별표를* 사용 하 여 여러 폴더 경계에서 경로를 캡처할 수 있습니다 (`/`).
* @No__t-0 경로 매개 변수 이름입니다.

자세한 내용은 <xref:fundamentals/routing>을 참조하세요.

## <a name="navlink-component"></a>NavLink 구성 요소

탐색 링크를 만들 때 HTML 하이퍼링크 요소 (`<a>`) 대신 `NavLink` 구성 요소를 사용 합니다. @No__t-0 구성 요소는 `href`이 현재 URL과 일치 하는지 여부에 따라 `active` CSS 클래스를 전환 하는 것을 제외 하 고는 `<a>` 요소 처럼 동작 합니다. @No__t-0 클래스를 통해 사용자는 표시 된 탐색 링크 중에서 활성 페이지가 되는 페이지를 이해할 수 있습니다.

다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

@No__t-2 요소의 `Match` 특성에 할당할 수 있는 두 가지 `NavLinkMatch` 옵션이 있습니다.

* `NavLinkMatch.All` @no__t 현재 URL과 일치 하는 경우 `NavLink`이 (가) 활성 상태입니다.
* `NavLinkMatch.Prefix` (*기본값*) &ndash; @no__t 현재 URL의 접두사와 일치 하는 경우 활성 상태입니다.

위의 예에서 Home `NavLink` `href=""`은 홈 URL과 일치 하 고 앱의 기본 기본 경로 URL에서 `active` CSS 클래스 (예: `https://localhost:5001/`)만 받습니다. 두 번째 `NavLink`은 사용자가 `MyComponent` 접두사 (예: `https://localhost:5001/MyComponent` 및 `https://localhost:5001/MyComponent/AnotherSegment`)를 사용 하 여 URL을 방문할 때 `active` 클래스를 수신 합니다.

추가 `NavLink` 구성 요소 특성은 렌더링 된 앵커 태그로 전달 됩니다. 다음 예제에서 `NavLink` 구성 요소는 `target` 특성을 포함 합니다.

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

렌더링 되는 HTML 태그는 다음과 같습니다.

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI 및 탐색 상태 도우미

@No__t-0을 사용 하 여 Uri로 작업 하 C# 고 코드에서 탐색 합니다. `NavigationManager`은 다음 표에 나와 있는 이벤트와 메서드를 제공 합니다.

| 멤버 | 설명 |
| ------ | ----------- |
| `Uri` | 현재 절대 URI를 가져옵니다. |
| `BaseUri` | 절대 URI를 생성 하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI (후행 슬래시 포함)를 가져옵니다. 일반적으로 `BaseUri`은 *wwwroot/index.html* (Blazor Weasembmbe) 또는 *Pages/_Host* (Blazor Server)의 문서 `<base>` 요소에 있는 `href` 특성에 해당 합니다. |
| `NavigateTo` | 지정 된 URI로 이동 합니다. @No__t-0 `true`입니다.<ul><li>클라이언트 쪽 라우팅이 무시 됩니다.</li><li>URI가 일반적으로 클라이언트 쪽 라우터에서 처리 되는지 여부와 상관 없이 브라우저는 서버에서 새 페이지를 강제로 로드 합니다.</li></ul> |
| `LocationChanged` | 탐색 위치가 변경 될 때 발생 하는 이벤트입니다. |
| `ToAbsoluteUri` | 상대 URI를 절대 URI로 변환 합니다. |
| `ToBaseRelativePath` | 기본 URI가 지정 된 경우 (예: 이전에 `GetBaseUri`에서 반환 된 URI)는 절대 URI를 기본 URI 접두사에 상대적인 URI로 변환 합니다. |

다음 구성 요소는 단추를 선택 하면 앱의 `Counter` 구성 요소로 이동 합니다.

```cshtml
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```
