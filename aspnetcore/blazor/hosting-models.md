---
title: ASP.NET Core Blazor 호스팅 모델
author: guardrex
description: Blazor 클라이언트 쪽 및 서버 쪽 호스팅 모델을 이해 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 9dd96ff6e3539bf1c3e932b33776b16d0fbb2d34
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68948293"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor 호스팅 모델

[Daniel Roth](https://github.com/danroth27)

Blazor는 [Webassembly](https://webassembly.org/)(*Blazor server side*) ASP.NET Core의 서버 쪽 또는 서버 쪽에서 브라우저에서 클라이언트 쪽을 실행하도록 디자인 된 웹 프레임 워크입니다. 호스팅 모델에 관계 없이 앱 및 구성 요소 모델은 *동일*합니다.

이 문서에서 설명 하는 호스팅 모델에 대 한 프로젝트를 만들려면 <xref:blazor/get-started>를 참조 하십시오.

## <a name="client-side"></a>클라이언트 쪽

Blazor에 대 한 주 호스팅 모델이 브라우저에서 클라이언트 쪽을 제대로 실행 하 고 있습니다. Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다. 해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다. UI 업데이트 및 이벤트 처리는 동일한 프로세스 내에서 발생 합니다. 응용 프로그램의 자산은 정적 콘텐츠를 클라이언트에 제공할 수 있는 웹 서버 또는 서비스에 정적 파일로 배포 됩니다.

![클라이언트 쪽 Blazor: Blazor 앱은 브라우저 내부의 UI 스레드에서 실행 됩니다.](hosting-models/_static/client-side.png)

클라이언트 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 다음 템플릿 중 하나를 사용 합니다.

* **Blazor (클라이언트 쪽)** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; 정적 파일 집합으로 배포 됩니다.
* **Blazor (ASP.NET Core 호스팅)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; ASP.NET Core 서버에서 호스팅됩니다. ASP.NET Core 앱은 Blazor 앱을 클라이언트에 제공 합니다. Blazor 클라이언트 쪽 앱은 web API 호출 또는 [SignalR](xref:signalr/introduction)를 사용 하 여 네트워크를 통해 서버와 상호 작용할 수 있습니다.

템플릿에는를 처리 하는 *blazor* 스크립트가 포함 됩니다.

* .NET 런타임, 앱 및 앱의 종속성 다운로드
* 앱을 실행 하기 위한 런타임 초기화

클라이언트 쪽 호스팅 모델에서는 다음과 같은 여러 가지 이점을 제공 합니다.

* .NET 서버 쪽 종속성이 없습니다. 클라이언트에 다운로드 한 후 앱이 완전히 작동 합니다.
* 클라이언트 리소스 및 기능은 완전히 활용 됩니다.
* 작업은 서버에서 클라이언트로 오프 로드 됩니다.
* ASP.NET Core 웹 서버는 앱을 호스트 하는 데 필요 하지 않습니다. 서버를 사용 하지 않는 배포 시나리오를 사용할 수 있습니다. 예를 들어 CDN에서 앱을 제공할 수 있습니다.

클라이언트 쪽 호스팅을 단점이 있습니다.

* 앱은 브라우저의 기능으로 제한 됩니다.
* 가능 클라이언트 하드웨어 및 소프트웨어 (예: Weasembomsupport)는 필수입니다.
* 다운로드 크기가 크고 앱을 로드 하는 데 시간이 오래 걸립니다.
* .NET 런타임 및 도구 지원의 완성도는 낮아집니다. 예를 들어 [.NET Standard](/dotnet/standard/net-standard) 지원 및 디버깅에는 제한 사항이 있습니다.

## <a name="server-side"></a>서버 쪽

서버 쪽 호스팅 모델을 사용 하면 앱이 ASP.NET Core 앱 내에서 서버에 실행 됩니다. UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.

![브라우저는 SignalR 연결을 통해 서버의 앱 (ASP.NET Core 앱 내에서 호스트)과 상호 작용 합니다.](hosting-models/_static/server-side.png)

서버 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 ASP.NET Core **Blazor Server 앱** 템플릿 ([dotnet new blazorserverside](/dotnet/core/tools/dotnet-new))을 사용 합니다. ASP.NET Core 앱은 서버 쪽 앱을 호스팅하고 클라이언트에서 연결 하는 SignalR 끝점을 만듭니다.

ASP.NET Core 앱은 추가할 앱 `Startup` 클래스를 참조 합니다.

* 서버 쪽 서비스.
* 요청 처리 파이프라인에 대 한 앱입니다.

*Blazor* 스크립트&dagger; 는 클라이언트 연결을 설정 합니다. 필요에 따라 앱 상태를 유지 하 고 복원 하는 것은 앱의 책임입니다 (예: 네트워크 연결이 끊어진 경우).

서버 쪽 호스팅 모델에서는 다음과 같은 여러 가지 이점을 제공 합니다.

* 다운로드 크기는 클라이언트 쪽 앱 보다 훨씬 더 작기 때문에 앱이 훨씬 더 빠르게 로드 됩니다.
* 앱은 .NET Core와 호환 되는 Api의 사용을 포함 하 여 서버 기능을 최대한 활용 합니다.
* 서버의 .NET Core는 앱을 실행 하는 데 사용 되므로 디버깅과 같은 기존 .NET 도구는 정상적으로 작동 합니다.
* 씬 클라이언트가 지원 됩니다. 예를 들어 서버 쪽 앱은 리소스 제한 장치에서 Weasembmbambmbsemba를 지원 하지 않는 브라우저에서 작동 합니다.
* 앱의 구성 요소 코드C# 를 포함 하 여 앱의 .net/code 베이스가 클라이언트에 제공 되지 않습니다.

서버 쪽 호스팅을 단점이 있습니다.

* 일반적으로 더 높은 대기 시간이 있습니다. 모든 사용자 상호 작용에는 네트워크 홉이 포함 됩니다.
* 오프 라인은 지원 되지 않습니다. 클라이언트 연결에 실패 하면 앱 작동이 중지 됩니다.
* 여러 사용자가 있는 앱의 경우에는 확장성이 어렵습니다. 서버는 여러 클라이언트 연결을 관리 하 고 클라이언트 상태를 처리 해야 합니다.
* 앱을 제공 하려면 ASP.NET Core 서버가 필요 합니다. 서버를 사용 하지 않는 배포 시나리오 (예: CDN에서 앱 제공)를 사용할 수 없습니다.

&dagger;*Blazor* 스크립트는 ASP.NET Core 공유 프레임 워크의 포함 리소스에서 제공 됩니다.

### <a name="reconnection-to-the-same-server"></a>동일한 서버에 다시 연결

Blazor 서버 쪽 앱은 서버에 대 한 활성 SignalR 연결이 필요 합니다. 연결이 끊어지면 앱이 서버에 다시 연결을 시도 합니다. 클라이언트의 상태가 아직 메모리에 있으면 클라이언트 세션이 상태 손실 없이 다시 시작 됩니다.
 
클라이언트에서 연결이 끊어진 것을 감지 하면 클라이언트에서 다시 연결을 시도 하는 동안 기본 UI가 표시 됩니다. 다시 연결이 실패 하는 경우 사용자에 게 다시 시도 하는 옵션이 제공 됩니다. UI를 사용자 지정 하려면 `components-reconnect-modal` *_Host* Razor 페이지 `id` 에서로 요소를 정의 합니다. 클라이언트는 연결 상태에 따라 다음 CSS 클래스 중 하나를 사용 하 여이 요소를 업데이트 합니다.
 
* `components-reconnect-show`&ndash; 연결이 끊어져서 클라이언트에서 다시 연결을 시도 했음을 나타내는 UI를 표시 합니다.
* `components-reconnect-hide`&ndash; 클라이언트에 활성 연결이 있으며 UI를 숨깁니다.
* `components-reconnect-failed`&ndash; 다시 연결 하지 못했습니다. 다시 연결을 다시 시도 하려면 `window.Blazor.reconnect()`를 호출 합니다.

### <a name="stateful-reconnection-after-prerendering"></a>렌더링 후 상태 저장 다시 연결
 
서버에 대 한 클라이언트 연결이 설정 되기 전에 서버에서 UI를 미리 렌더링 하도록 Blazor 서버 쪽 앱이 기본적으로 설정 되어 있습니다. 이는 *_Host* Razor 페이지에 설정 되어 있습니다.
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
클라이언트는 앱을 미리 렌더링 하는 데 사용 된 상태와 동일한 상태를 사용 하 여 서버에 다시 연결 합니다. 앱의 상태가 아직 메모리에 있는 경우 SignalR 연결이 설정 된 후에 구성 요소 상태가 다시 발생 하지 않습니다.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링
 
상태 저장 대화형 구성 요소는 Razor 페이지 또는 보기에 추가할 수 있습니다. 페이지 또는 뷰가 렌더링 되 면 구성 요소는 미리 렌더링 된 됩니다. 그런 다음 상태가 아직 메모리에 있는 한 클라이언트 연결이 설정 되 면 앱은 구성 요소 상태에 다시 연결 합니다.
 
예를 들어 다음 Razor 페이지는 폼을 `Counter` 사용 하 여 지정 된 초기 카운트를 사용 하 여 구성 요소를 렌더링 합니다.
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a>앱이 사전 렌더링 되는 경우 검색
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>Blazor 서버 쪽 앱에 대 한 SignalR 클라이언트 구성
 
경우에 따라 Blazor 서버 쪽 앱에서 사용 하는 SignalR 클라이언트를 구성 해야 합니다. 예를 들어 SignalR 클라이언트에 대 한 로깅을 구성 하 여 연결 문제를 진단할 수 있습니다.
 
*Pages/_Host* 파일에서 SignalR client를 구성 하려면 다음을 수행 합니다.

* *Blazor 스크립트* 에 대 한 `<script>` 태그에 특성을추가합니다.`autostart="false"`
* 을 `Blazor.start` 호출 하 고 SignalR builder를 지정 하는 구성 개체를 전달 합니다.
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/get-started>
* <xref:signalr/introduction>
