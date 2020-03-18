---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법과 NavLink 구성 요소를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 32459f9f42220b01ce04e6444a9bb4a9592ee2da
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649239"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor 라우팅

[Luke Latham](https://github.com/guardrex)으로

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

요청을 라우팅하는 방법과 Blazor 앱에서 `NavLink` 구성 요소를 사용하여 탐색 링크를 만드는 방법을 알아봅니다.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core 엔드포인트 라우팅 통합

Blazor 서버는 [ASP.NET Core 엔드포인트 라우팅](xref:fundamentals/routing)에 통합됩니다. `Startup.Configure`의 `MapBlazorHub`를 사용하여 대화형 구성 요소에 대해 들어오는 연결을 허용하도록 ASP.NET Core 앱을 구성합니다.

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

가장 일반적인 구성은 Blazor 서버 앱의 서버 쪽 호스트 역할을 하는 Razor 페이지로 모든 요청을 라우팅하는 것입니다. 규칙에 따라 ‘호스트’ 페이지의 이름은 일반적으로 *_Host.cshtml*입니다.  호스트 파일에 지정된 경로는 경로 일치 시 낮은 우선순위로 작동하기 때문에 ‘대체 경로’라고 합니다.  일치하는 다른 경로가 없는 경우 대체(fallback) 경로가 사용됩니다. 이렇게 하면 앱이 Blazor 서버 앱을 방해하지 않고 다른 컨트롤러와 페이지를 사용할 수 있습니다.

## <a name="route-templates"></a>경로 템플릿

`Router` 구성 요소는 지정된 경로를 사용하여 각 구성 요소로 라우팅할 수 있도록 합니다. `Router` 구성 요소는 *App.razor* 파일에 표시됩니다.

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

`@page` 지시문을 포함하는 *.razor* 파일을 컴파일하면, 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 제공됩니다.

런타임에 `RouteView` 구성 요소는 다음을 수행합니다.

* 원하는 매개 변수와 함께 `Router`에서 `RouteData`를 받습니다.
* 지정된 매개 변수를 사용하여 지정된 구성 요소를 해당 레이아웃(또는 선택적 기본 레이아웃)으로 렌더링합니다.

필요한 경우, 레이아웃을 지정하지 않는 구성 요소에 사용할 `DefaultLayout` 매개 변수를 레이아웃 클래스로 지정할 수 있습니다. 기본 Blazor 템플릿은 `MainLayout` 구성 요소를 지정합니다. *MainLayout.razor*는 템플릿 프로젝트의 *Shared* 폴더에 있습니다. 레이아웃에 대한 자세한 내용은 <xref:blazor/layouts>을 참조하세요.

한 구성 요소에 여러 개의 경로 템플릿을 적용할 수 있습니다. 다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대한 요청에 응답합니다.

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> URL이 올바르게 확인되려면 앱의 *wwwroot/index.html* 파일(Blazor WebAssembly) 또는 *Pages/_Host.cshtml* 파일(Blazor 서버)에 `<base>` 태그가 있고, 앱 기본 경로가 `href` 특성에 지정되어 있어야 합니다(`<base href="/">`). 자세한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>를 참조하세요.

## <a name="provide-custom-content-when-content-isnt-found"></a>콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공

`Router` 구성 요소를 사용하면 요청된 경로의 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.

*App.razor* 파일에서 `Router` 구성 요소의 `NotFound` 템플릿 매개 변수에 사용자 지정 콘텐츠를 설정합니다.

```razor
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

`<NotFound>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목을 포함할 수 있습니다. `NotFound` 콘텐츠에 기본 레이아웃을 적용하려면 <xref:blazor/layouts>을 참조하세요.

## <a name="route-to-components-from-multiple-assemblies"></a>여러 어셈블리에서 구성 요소로 라우팅

`AdditionalAssemblies` 매개 변수를 사용하여 라우팅 가능한 구성 요소를 검색할 때 고려할 추가 어셈블리를 `Router` 구성 요소에 대해 지정할 수 있습니다. 지정한 어셈블리는 `AppAssembly`에서 지정한 어셈블리에 추가로 고려됩니다. 다음 예제에서 `Component1`은 참조된 클래스 라이브러리에서 정의된 라우팅 가능한 구성 요소입니다. 다음 `AdditionalAssemblies` 예제에서는 `Component1`에 대해 라우팅 지원이 생성됩니다.

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>경로 매개 변수

라우터는 경로 매개 변수를 사용하여 해당 구성 요소 매개 변수를 동일한 이름(대/소문자 구분 안 함)으로 채웁니다.

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

선택적 매개 변수는 지원되지 않습니다. 위의 예제에서는 두 개의 `@page` 지시문이 적용되었습니다. 첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다. 두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용하고 `Text` 속성에 값을 할당합니다.

## <a name="route-constraints"></a>경로 제약 조건

경로 제약 조건은 경로 세그먼트의 형식 일치를 구성 요소에 적용합니다.

다음 예제에서 `Users` 구성 요소의 경로는 다음과 같은 경우에만 일치합니다.

* 요청 URL에 `Id` 경로 세그먼트가 있는 경우
* `Id` 세그먼트가 정수(`int`)인 경우

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

다음 표에 나와 있는 경로 제약 조건을 사용할 수 있습니다. 고정 문화권과 일치하는 경로 제약 조건에 대한 자세한 내용은 표 아래에 있는 경고를 참조하세요.

| 제약 조건 | 예제           | 일치하는 예제                                                                  | 고정<br>culture<br>일치 |
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

### <a name="routing-with-urls-that-contain-dots"></a>점이 포함된 URL을 사용한 라우팅

Blazor 서버 앱에서 *_Host.cshtml*의 기본 경로는 `/`(`@page "/"`)입니다. 점(`.`)이 포함된 요청 URL은 URL이 파일을 요청하는 것 같으므로 기본 경로와 일치되지 않습니다. Blazor 앱은 존재하지 않는 정적 파일에 대해 ‘404 - 찾을 수 없음’ 응답을 반환합니다.  점이 포함된 경로를 사용하려면 다음 경로 템플릿을 통해 *_Host.cshtml*을 구성합니다.

```cshtml
@page "/{**path}"
```

`"/{**path}"` 템플릿에는 다음이 포함되어 있습니다.

* 슬래시(`/`)를 인코딩하지 않고 여러 폴더 경계에 걸쳐 있는 경로를 캡처하기 위한 이중 별표 *catch-all* 구문(`**`)
* `path` 경로 매개 변수 이름

> [!NOTE]
> *Catch-all* 매개 변수 구문(`*`/`**`)은 Razor 구성 요소( *.razor*)에서 지원되지 **않습니다**.

자세한 내용은 <xref:fundamentals/routing>를 참조하세요.

## <a name="navlink-component"></a>NavLink 구성 요소

탐색 링크를 만드는 경우 HTML 하이퍼링크 요소(`<a>`) 대신 `NavLink` 구성 요소를 사용합니다. `NavLink` 구성 요소는 `href`가 현재 URL과 일치하는지 아닌지에 따라 `active` CSS 클래스를 전환한다는 점을 제외하고 `<a>` 요소처럼 동작합니다. `active` 클래스는 사용자가 표시되는 탐색 링크 중에서 활성 페이지를 파악하는 데 도움이 됩니다.

다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 다음 두 가지 `NavLinkMatch` 옵션이 있습니다.

* `NavLinkMatch.All` - 현재 URL 전체와 일치하는 경우 `NavLink`가 활성 상태입니다.
* `NavLinkMatch.Prefix`(‘기본값’) - 현재 URL의 접두사와 일치하는 경우 `NavLink`가 활성 상태입니다. 

위의 예제에서 홈 `NavLink` `href=""`는 홈 URL과 일치하고, 앱의 기본 경로 URL(예: `https://localhost:5001/`)에 있는 `active` CSS 클래스만 받습니다. 두 번째 `NavLink`는 사용자가 `MyComponent` 접두사를 포함하는 URL(예: `https://localhost:5001/MyComponent` 및 `https://localhost:5001/MyComponent/AnotherSegment`)을 방문할 때 `active` 클래스를 받습니다.

추가 `NavLink` 구성 요소 특성이 렌더링된 앵커 태그로 전달됩니다. 다음 예제에서 `NavLink` 구성 요소는 `target` 특성을 포함합니다.

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

다음 HTML 태그가 렌더링됩니다.

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI 및 탐색 상태 도우미

`Microsoft.AspNetCore.Components.NavigationManager`를 사용하여 C# 코드로 URI 및 탐색 작업을 수행할 수 있습니다. `NavigationManager`는 다음 표에 나와 있는 이벤트와 메서드를 제공합니다.

| 멤버 | 설명 |
| ------ | ----------- |
| `Uri` | 현재 절대 URI를 가져옵니다. |
| `BaseUri` | 절대 URI를 생성하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI(후행 슬래시 포함)를 가져옵니다. 일반적으로 `BaseUri`는 *wwwroot/index.html*(Blazor WebAssembly) 또는 *Pages/_Host.cshtml*(Blazor 서버)에 있는 문서 `<base>` 요소의 `href` 특성에 해당합니다. |
| `NavigateTo` | 지정한 URI로 이동합니다. `forceLoad`가 `true`인 경우<ul><li>클라이언트 쪽 라우팅이 무시됩니다.</li><li>클라이언트 쪽 라우터에서 URI를 정상적으로 처리했는지와 상관없이 브라우저에서 서버의 새 페이지를 강제로 로드합니다.</li></ul> |
| `LocationChanged` | 탐색 위치가 변경된 경우에 발생하는 이벤트입니다. |
| `ToAbsoluteUri` | 상대 URI를 절대 URI로 변환합니다. |
| `ToBaseRelativePath` | 기본 URI(예: 이전에 `GetBaseUri`에서 반환된 URI)가 제공된 경우, 절대 URI를 기본 URI 접두사의 상대 URI로 변환합니다. |

단추를 선택하면 다음 구성 요소가 앱의 `Counter` 구성 요소로 이동합니다.

```razor
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
