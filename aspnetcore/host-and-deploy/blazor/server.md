---
title: ASP.NET Core Blazor 서버 호스트 및 배포
author: guardrex
description: ASP.NET Core를 사용하여 Blazor 서버 앱을 호스트 및 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 693d7ff67bad3a0c5bd050b795833763056ed511
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378818"
---
# <a name="host-and-deploy-blazor-server"></a>Blazor 서버 호스트 및 배포

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>호스트 구성 값

[Blazor 서버 앱](xref:blazor/hosting-models#blazor-server)은 [일반 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.

## <a name="deployment"></a>배포

[Blazor 서버 호스팅 모델](xref:blazor/hosting-models#blazor-server)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다. UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.

ASP.NET Core 앱을 호스팅할 수 있는 웹 서버가 필요합니다. Visual Studio에는 **Blazor 서버 앱** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorserverside` 템플릿)이 포함됩니다.

## <a name="scalability"></a>확장성

Blazor 서버 앱에 사용 가능한 인프라를 최대한 활용하도록 배포를 계획합니다. Blazor 서버 앱 확장성을 해결하려면 다음 리소스를 참조하세요.

* [Blazor 서버 앱 기본 사항](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>배포 서버

단일 서버의 확장성(규모 확대)을 고려할 때 앱에 사용할 수 있는 메모리는 사용자 요구가 늘어남에 따라 앱이 소진하는 첫 번째 리소스일 가능성이 높습니다. 서버에서 사용 가능한 메모리는 다음 사항에 영향을 줍니다.

* 서버가 지원할 수 있는 활성 회로 수
* 클라이언트의 UI 대기 시간

안전하고 확장 가능한 Blazor 서버 앱을 빌드하는 방법에 대한 지침은 <xref:security/blazor/server>을 참조하세요.

각 회로는 최소 *Hello World*와 같은 앱에 약 250KB의 메모리를 사용합니다. 회로의 크기는 앱의 코드 및 각 구성 요소와 연결된 상태 유지 관리 요구 사항에 따라 달라집니다. 애플리케이션 및 인프라를 개발하는 도중에 리소스 요구를 측정하는 것이 좋지만, 다음 기준은 배포 대상을 계획할 때 출발점으로 삼을 수 있습니다. 앱에서 5,000명의 동시 사용자를 지원할 것으로 예상되는 경우 앱에 최소 1.3GB(또는 사용자당 273KB)의 서버 메모리를 예산하는 것이 좋습니다.

### <a name="signalr-configuration"></a>SignalR 구성

Blazor 서버 앱은 ASP.NET Core SignalR을 사용하여 브라우저와 통신합니다. [SignalR의 호스팅 및 크기 조정 조건](xref:signalr/publish-to-azure-web-app)이 Blazor 서버 앱에 적용됩니다.

Blazor는 짧은 대기 시간, 안정성 및 [보안](xref:signalr/security) 덕분에 WebSocket을 SignalR 전송으로 사용하는 경우에 가장 효과적입니다. WebSocket을 사용할 수 없거나 앱이 긴 폴링을 사용하도록 명시적으로 구성된 경우 SignalR에서 긴 폴링을 사용합니다. Azure App Service에 배포하는 경우 서비스에 대한 Azure Portal 설정에서 WebSocket을 사용하도록 앱을 구성합니다. Azure App Service에 대해 앱을 구성하는 방법에 대한 자세한 내용은 [SignalR 게시 지침](xref:signalr/publish-to-azure-web-app)을 참조하세요.

Blazor 서버 앱에 [Azure SignalR Service](/azure/azure-signalr)를 사용하는 것이 좋습니다. 이 서비스를 사용하면 Blazor 서버 앱을 다수의 동시 SignalR 연결로 확장할 수 있습니다. 또한 SignalR 서비스의 글로벌 및 고성능 데이터 센터는 지리적 위치로 인한 대기 시간을 줄이는 데 큰 도움이 됩니다. 앱을 구성하고 원하는 경우 Azure SignalR Service를 프로비전하려면 다음 단계를 따릅니다.

* Visual Studio에 Blazor 서버 앱을 위한 Azure 앱 게시 프로필을 만듭니다.
* **Azure SignalR Service** 종속성을 프로필에 추가합니다. 앱에 할당할 기존 Azure SignalR Service 인스턴스가 Azure 구독에 없는 경우 **새 Azure SignalR Service 인스턴스 만들기**를 선택하여 새 서비스 인스턴스를 프로비전합니다.
* Azure에 앱 게시

### <a name="measure-network-latency"></a>네트워크 대기 시간 측정

[JS interop](xref:blazor/javascript-interop)은 다음 예제와 같이 네트워크 대기 시간을 측정하는 데 사용할 수 있습니다.

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

적절한 UI 환경을 위해 UI 대기 시간을 250ms 이하로 유지하는 것이 좋습니다.
