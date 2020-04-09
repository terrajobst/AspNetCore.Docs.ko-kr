---
title: 보안 ASP.NET Blazor 핵심 서버 앱
author: guardrex
description: Server 앱에 대한 Blazor 보안 위협을 완화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626018"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>보안 ASP.NET 코어 블레이저 서버 앱

[하비에르 칼바로 넬슨](https://github.com/javiercn)

Blazor Server 앱은 서버와 클라이언트가 수명이 긴 관계를 유지하는 *상태 저장* 데이터 처리 모델을 채택합니다. 영구 상태는 잠재적으로 수명이 긴 연결을 포괄할 수 있는 [회로에](xref:blazor/state-management)의해 유지됩니다.

사용자가 Blazor Server 사이트를 방문하면 서버가 서버 메모리에 회로를 만듭니다. 회로는 사용자가 UI에서 단추를 선택할 때와 같이 렌더링할 콘텐츠를 브라우저에 나타내고 이벤트에 응답합니다. 이러한 작업을 수행하기 위해 회로는 사용자의 브라우저에서 JavaScript 함수를 호출하고 서버의 .NET 메서드를 호출합니다. 이 양방향 자바 스크립트 기반 상호 작용은 [자바 스크립트 인터옵 (JS interop)이라고합니다.](xref:blazor/call-javascript-from-dotnet)

JS 인터옵은 인터넷을 통해 발생하고 클라이언트는 원격 브라우저를 사용하기 때문에 Blazor Server 앱은 대부분의 웹 앱 보안 문제를 공유합니다. 이 항목에서는 Blazor Server 앱에 대한 일반적인 위협에 대해 설명하고 인터넷 연결 앱에 중점을 둔 위협 완화 지침을 제공합니다.

내부 회사 네트워크 또는 인트라넷과 같이 제한된 환경에서는 다음과 같은 완화 지침 중 일부가 있습니다.

* 제한된 환경에는 적용되지 않습니다.
* 제한된 환경에서 는 보안 위험이 낮기 때문에 구현할 가치가 없습니다.

## <a name="blazor-server-project-template"></a>블레이저 서버 프로젝트 템플릿

Blazor Server 프로젝트 템플릿은 프로젝트를 만들 때 인증을 위해 구성할 수 있습니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.

**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.

다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.

* **인증 없음**
* **개별 사용자 계정** &ndash; 사용자 계정은 다음과 같이 저장할 수 있습니다.
  * ASP.NET Core의 [ID](xref:security/authentication/identity) 시스템을 사용하여 앱에 저장
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용
* **직장 또는 학교 계정**
* **Windows 인증**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.

| 인증 메커니즘                                                                 | `{AUTHENTICATION}` 값 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| 인증 없음                                                                        | `None`                   |
| 개인<br>ASP.NET Core ID를 사용하여 앱에 저장된 사용자                        | `Individual`             |
| 개인<br>[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자 | `IndividualB2C`          |
| 회사 또는 학교 계정<br>단일 테넌트에 대한 조직 인증            | `SingleOrg`              |
| 회사 또는 학교 계정<br>여러 테넌트에 대한 조직 인증           | `MultiOrg`               |
| Windows 인증                                                                   | `Windows`                |

이 명령은 `{APP NAME}` 자리 표시자에 제공된 값으로 이름을 지정하여 폴더를 만들고, 폴더 이름을 앱 이름으로 사용합니다. 자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. 이 문서에서 Mac 지침에 <xref:blazor/get-started> 대한 Visual Studio를 따릅니다.

1. 새 **Blazor 서버 앱 구성** 단계에서 **인증** 드롭다운에서 개별 **인증(인앱)을** 선택합니다.

1. 응용 프로그램은 ASP.NET 코어 ID와 응용 프로그램에 저장된 개별 사용자를 위해 만들어집니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

<xref:blazor/get-started> 인증 메커니즘을 사용하여 새 Blazor Server 프로젝트를 만들려면 문서의 .NET Core CLI 지침을 따릅니다.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.

| 인증 메커니즘                                                                 | `{AUTHENTICATION}` 값 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| 인증 없음                                                                        | `None`                   |
| 개인<br>ASP.NET Core ID를 사용하여 앱에 저장된 사용자                        | `Individual`             |
| 개인<br>[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자 | `IndividualB2C`          |
| 회사 또는 학교 계정<br>단일 테넌트에 대한 조직 인증            | `SingleOrg`              |
| 회사 또는 학교 계정<br>여러 테넌트에 대한 조직 인증           | `MultiOrg`               |
| Windows 인증                                                                   | `Windows`                |

이 명령은 `{APP NAME}` 자리 표시자에 제공된 값으로 이름을 지정하여 폴더를 만들고, 폴더 이름을 앱 이름으로 사용합니다. 자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>블레이저 서버 앱에 토큰 전달

일반 Razor 페이지 또는 MVC 앱과 마찬가지로 Blazor Server 앱을 인증합니다. 토큰을 프로비전하고 인증 쿠키에 저장합니다. 다음은 그 예입니다.

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

전체 `Startup.ConfigureServices` 예제를 포함한 샘플 코드의 경우 [서버 측 Blazor 응용 프로그램에 대한 Passing 토큰을](https://github.com/javiercn/blazor-server-aad-sample)참조하십시오.

액세스 및 새로 고침 토큰을 사용하여 초기 앱 상태로 전달할 클래스를 정의합니다.

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Blazor 앱 내에서 DI에서 토큰을 해결하는 데 사용할 수 있는 **범위가 되는** 토큰 공급자 서비스를 정의합니다.

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

에서 `Startup.ConfigureServices`다음에 대한 서비스 추가

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

*_Host.cshtml* 파일에서 응용 프로그램에 `InitialApplicationState` 매개 변수로 만들고 인스턴스를 만듭니다.

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

`App` 구성*요소(App.razor)에서*서비스를 해결하고 매개 변수의 데이터로 초기화합니다.

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

보안 API 요청을 하는 서비스에서 토큰 공급자를 삽입 하고 토큰을 검색하여 API를 호출합니다.

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>자원 고갈

클라이언트가 서버와 상호 작용하여 서버가 과도한 리소스를 소비하게 할 때 리소스 가 소진될 수 있습니다. 과도한 리소스 소비는 주로 다음과 같은 영향을 미칩니다.

* [Cpu](#cpu)
* [메모리](#memory)
* [클라이언트 연결](#client-connections)

DoS(서비스 거부) 공격은 일반적으로 앱 또는 서버의 리소스를 소진하려고 합니다. 그러나 리소스 소모가 반드시 시스템에 대한 공격의 결과인 것은 아닙니다. 예를 들어 사용자 수요가 많기 때문에 유한한 리소스가 소진될 수 있습니다. DoS는 [서비스 거부(DoS) 공격](#denial-of-service-dos-attacks) 섹션에서 자세히 다룹니다.

데이터베이스 및 파일 핸들(파일을 읽고 쓰는 데 사용됨)과 같은 Blazor 프레임워크 외부의 리소스도 리소스 소모가 발생할 수 있습니다. 자세한 내용은 <xref:performance/performance-best-practices>을 참조하세요.

### <a name="cpu"></a>CPU

하나 이상의 클라이언트가 서버가 집중적인 CPU 작업을 수행하도록 강제할 때 CPU 소모가 발생할 수 있습니다.

예를 *들어, 피보나치 숫자를*계산하는 Blazor Server 앱을 생각해 보십시오. 피본나치 번호는 피본아치 시퀀스에서 생성되며, 시퀀스의 각 숫자는 앞의 두 숫자의 합계입니다. 답변에 도달하는 데 필요한 작업량은 시퀀스의 길이와 초기 값의 크기에 따라 달라집니다. 앱이 클라이언트의 요청에 제한을 두지 않으면 CPU 집약적인 계산이 CPU의 시간을 지배하고 다른 작업의 성능을 저하시킬 수 있습니다. 과도한 리소스 소비는 가용성에 영향을 미치는 보안 문제입니다.

CPU 소모는 모든 공용 앱에 대한 관심사입니다. 일반 웹 앱에서는 요청 및 연결이 보호 장치로 시간 시간이 부족하지만 Blazor Server 앱은 동일한 보호 장치를 제공하지 않습니다. Blazor Server 앱에는 CPU집약적인 작업을 수행하기 전에 적절한 검사와 제한이 포함되어야 합니다.

### <a name="memory"></a>메모리

하나 이상의 클라이언트가 서버에 많은 양의 메모리를 사용하도록 강제할 때 메모리 소모가 발생할 수 있습니다.

예를 들어 항목 목록을 허용하고 표시하는 구성 요소가 있는 Blazor-server 쪽 앱을 생각해 보겠습니다. Blazor 앱에서 허용되는 항목 수 또는 클라이언트로 다시 렌더링되는 항목 수에 제한을 두지 않는 경우 메모리 집약적인 처리 및 렌더링이 서버의 성능이 저하될 수 있는 시점까지 서버의 메모리를 지배할 수 있습니다. 서버가 충돌한 것으로 보이는 지점까지 충돌하거나 속도가 느려질 수 있습니다.

서버의 잠재적인 메모리 소모 시나리오와 관련된 항목 목록을 유지 관리하고 표시하기 위한 다음 시나리오를 고려하십시오.

* `List<MyItem>` 속성 또는 필드의 항목은 서버의 메모리를 사용합니다. 앱에서 항목 목록이 무한하게 증가하도록 허용하면 서버의 메모리가 부족할 위험이 있습니다. 메모리가 부족하면 현재 세션이 종료(충돌)되고 해당 서버 인스턴스의 모든 동시 세션이 메모리 부족 예외를 받습니다. 이 시나리오가 발생하지 않도록 하려면 앱에서 동시 사용자에 대한 항목 제한을 부과하는 데이터 구조를 사용해야 합니다.
* 페이징 스키마가 렌더링에 사용되지 않으면 서버는 UI에 표시되지 않는 개체에 대해 추가 메모리를 사용합니다. 항목 수에 제한이 없으면 메모리 요구로 사용 가능한 서버 메모리가 소모될 수 있습니다. 이 시나리오를 방지하려면 다음 방법 중 하나를 사용합니다.
  * 렌더링 할 때 페이지 가을 목록을 사용합니다.
  * 처음 100~1,000개의 항목만 표시하고 사용자가 검색 조건을 입력하여 표시된 항목 이외의 항목을 찾아야 합니다.
  * 고급 렌더링 시나리오의 경우 *가상화를*지원하는 목록 또는 그리드를 구현합니다. 가상화를 사용하면 사용자에게 현재 표시되는 항목의 하위 집합만 나열합니다. 사용자가 UI에서 스크롤 막대와 상호 작용하면 구성 요소는 표시에 필요한 항목만 렌더링합니다. 현재 디스플레이에 필요하지 않은 항목은 보조 저장소에 보관할 수 있으며 이는 이상적인 방법입니다. 표시되지 않은 항목은 메모리에 보관할 수도 있습니다.

Blazor Server 앱은 WPF, Windows Forms 또는 Blazor WebAssembly와 같은 상태 저장 앱에 대한 다른 UI 프레임워크와 유사한 프로그래밍 모델을 제공합니다. 주요 차이점은 여러 UI 프레임워크에서 앱에서 사용하는 메모리가 클라이언트에 속하며 개별 클라이언트에만 영향을 미친다는 점입니다. 예를 들어 Blazor WebAssembly 앱은 클라이언트에서 전적으로 실행되며 클라이언트 메모리 리소스만 사용합니다. Blazor Server 시나리오에서 앱에서 사용하는 메모리는 서버에 속하며 서버 인스턴스의 클라이언트 간에 공유됩니다.

서버 측 메모리 요구는 모든 Blazor Server 앱에서 고려해야 합니다. 그러나 대부분의 웹 앱은 상태 비수상태이며 요청을 처리하는 동안 사용된 메모리는 응답이 반환될 때 해제됩니다. 일반적으로 클라이언트가 클라이언트 연결을 유지하는 다른 서버 쪽 앱과 같이 언바운드 양의 메모리를 할당하도록 허용하지 마십시오. Blazor Server 앱에서 사용하는 메모리는 단일 요청보다 더 긴 시간 동안 유지됩니다.

> [!NOTE]
> 개발 중에 프로파일러를 사용하거나 캡처한 추적을 사용하여 클라이언트의 메모리 요구를 평가할 수 있습니다. 프로파일러 또는 추적은 특정 클라이언트에 할당된 메모리를 캡처하지 않습니다. 개발 중에 특정 클라이언트의 메모리 사용을 캡처하려면 덤프를 캡처하고 사용자의 회로에 루팅된 모든 개체의 메모리 수요를 검사합니다.

### <a name="client-connections"></a>클라이언트 연결

하나 이상의 클라이언트가 서버에 너무 많은 동시 연결을 열어 다른 클라이언트가 새 연결을 설정하지 못하도록 하면 연결이 끊어지기도 합니다.

Blazor 클라이언트는 세션당 단일 연결을 설정하고 브라우저 창이 열려 있는 동안 연결을 열어 두십시오. 모든 연결을 유지 관리하는 서버에 대한 요구는 Blazor 앱에만 국한되지 않습니다. Blazor Server 앱의 연결의 지속적인 특성과 상태 저장 특성을 고려할 때 연결 고갈은 앱 가용성에 더 큰 위험이 있습니다.

기본적으로 Blazor Server 앱의 사용자당 연결 수에는 제한이 없습니다. 앱에 연결 제한이 필요한 경우 다음 방법 중 하나 이상을 수행하십시오.

* 권한이 없는 사용자가 앱에 연결하는 기능을 자연스럽게 제한하는 인증이 필요합니다. 이 시나리오가 효과적이려면 사용자가 새 사용자를 즉시 프로비전할 수 없습니다.
* 사용자당 연결 수를 제한합니다. 연결 제한은 다음 방법을 통해 수행할 수 있습니다. 합법적인 사용자가 앱에 액세스할 수 있도록 주의하십시오(예: 클라이언트의 IP 주소에 따라 연결 제한이 설정된 경우).
  * 앱 수준에서:
    * 끝점 라우팅 확장성.
    * 앱에 연결하고 사용자당 활성 세션을 추적하려면 인증이 필요합니다.
    * 한도에 도달하면 새 세션을 거부합니다.
    * 프록시 WebSocket 클라이언트에서 앱으로의 연결을 다중화하는 [Azure SignalR 서비스와](/azure/azure-signalr/signalr-overview) 같은 프록시를 사용하여 앱에 연결합니다. 이렇게 하면 단일 클라이언트가 설정할 수 있는 것보다 더 큰 연결 용량을 가진 앱이 제공되어 클라이언트가 서버에 대한 연결을 모두 소모하지 못하게 됩니다.
  * 서버 수준에서: 앱 앞에 있는 프록시/게이트웨이를 사용합니다. 예를 들어 [Azure Front Door를](/azure/frontdoor/front-door-overview) 사용하면 앱에 대한 웹 트래픽의 전역 라우팅을 정의, 관리 및 모니터링할 수 있습니다.

## <a name="denial-of-service-dos-attacks"></a>서비스 거부(DoS) 공격

DoS(서비스 거부) 공격에는 클라이언트가 하나 이상의 리소스를 소모하여 앱을 사용할 수 없게 만듭니다. Blazor Server 앱에는 몇 가지 기본 제한이 포함되어 있으며 DoS 공격으로부터 보호하기 위해 다른 ASP.NET 코어 및 SignalR 제한을 사용합니다.

| 블레이저 서버 앱 제한                            | Description | 기본값 |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | 지정된 서버가 한 번에 메모리에 보유하는 연결이 끊긴 회로의 최대 수입니다. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | 연결이 끊긴 회로가 분리되기 전에 메모리에 보관되는 최대 시간입니다. | 3분 |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | 비동기 JavaScript 함수 호출을 타이밍 아웃하기 전에 서버가 대기하는 최대 시간입니다. | 1분 |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | 승인되지 않은 최대 렌더 일괄 처리 수는 서버가 강력한 재연결을 지원하기 위해 지정된 시간에 회로당 메모리에 유지합니다. 제한에 도달하면 서버는 클라이언트에서 하나 이상의 일괄 처리가 승인될 때까지 새 렌더 일괄 처리를 생성하지 않습니다. | 10 |


| 시그널R 및 ASP.NET 코어 한계             | Description | 기본값 |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | 개별 메시지에 대한 메시지 크기입니다. | 32KB |

## <a name="interactions-with-the-browser-client"></a>브라우저와의 상호 작용(클라이언트)

클라이언트는 JS interop 이벤트 디스패치 및 렌더링 완료를 통해 서버와 상호 작용합니다. JS 인터옵 통신은 자바 스크립트와 .NET 사이에 두 가지 방법을 간다 :

* 브라우저 이벤트는 클라이언트에서 서버로 비동기 방식으로 전달됩니다.
* 서버는 필요에 따라 UI를 비동기적으로 다시 렌더링합니다.

### <a name="javascript-functions-invoked-from-net"></a>.NET에서 호출된 자바스크립트 함수

.NET 메서드에서 자바스크립트로 의 호출하는 경우:

* 모든 호출에는 구성 가능한 시간 시간이 있으며, 그 <xref:System.OperationCanceledException> 후에는 실패하여 호출자에게 a를 반환합니다.
  * 1분의 호출()에`CircuitOptions.JSInteropDefaultCallTimeout`대한 기본 시간 시간이 있습니다. 이 제한을 구성하려면 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>을 참조하십시오.
  * 호출별로 취소를 제어하기 위해 취소 토큰을 제공할 수 있습니다. 가능한 경우 기본 호출 시간 시간에 의존하고 취소 토큰이 제공되는 경우 클라이언트에 대한 모든 호출을 시간 바인딩합니다.
* 자바 스크립트 호출의 결과를 신뢰할 수 없습니다. 브라우저에서 실행 중인 앱 클라이언트는 Blazor 호출할 JavaScript 함수를 검색합니다. 함수가 호출되고 결과 또는 오류가 생성됩니다. 악의적인 클라이언트는 다음을 시도할 수 있습니다.
  * 자바 스크립트 함수에서 오류를 반환하여 응용 프로그램에 문제가 발생합니다.
  * JavaScript 함수에서 예기치 않은 결과를 반환하여 서버에서 의도하지 않은 동작을 유도합니다.

위의 시나리오에 대비하기 위해 다음 예방 조치를 취하십시오.

* 호출 중에 발생할 수 있는 오류를 고려하기 위해 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문 내에서 JS interop 호출을 래핑합니다. 자세한 내용은 <xref:blazor/handle-errors#javascript-interop>을 참조하세요.
* 작업을 수행하기 전에 오류 메시지를 포함하여 JS interop 호출에서 반환된 데이터의 유효성을 검사합니다.

### <a name="net-methods-invoked-from-the-browser"></a>브라우저에서 호출된 .NET 메서드

JavaScript에서 .NET 메서드로 의 호출을 신뢰하지 마십시오. .NET 메서드가 JavaScript에 노출되면 .NET 메서드가 호출되는 방식을 고려하십시오.

* 앱에 대한 공용 끝점과 마찬가지로 JavaScript에 노출된 모든 .NET 메서드를 처리합니다.
  * 입력의 유효성을 검사합니다.
    * 값이 예상 범위 내에 있는지 확인합니다.
    * 사용자에게 요청된 작업을 수행할 수 있는 권한이 있는지 확인합니다.
  * .NET 메서드 호출의 일부로 과도한 양의 리소스를 할당하지 마십시오. 예를 들어, CPU 및 메모리 사용에 대한 검사 및 배치 제한을 수행합니다.
  * 정적 및 인스턴스 메서드가 JavaScript 클라이언트에 노출될 수 있음을 고려하십시오. 디자인에서 적절한 제약 조건으로 상태를 공유해야 하는 경우가 아니면 세션 간에 상태를 공유하지 마십시오.
    * 예를 들어 DI(종속성 주입)를 통해 원래 생성된 개체를 통해 `DotNetReference` 노출된 메서드의 경우 개체를 범위 가 있는 개체로 등록해야 합니다. 이는 서버 앱이 Blazor 사용하는 모든 DI 서비스에 적용됩니다.
    * 정적 메서드의 경우 앱이 서버 인스턴스의 모든 사용자에 대해 상태 별 디자인을 명시적으로 공유하지 않는 한 클라이언트에 범위를 지정할 수 없는 상태를 설정하지 마십시오.
  * 사용자가 제공한 데이터를 매개 변수로 JavaScript 호출로 전달하지 마십시오. 매개 변수로 데이터를 전달하는 것이 절대적으로 필요한 경우 JavaScript 코드가 [XSS(교차 사이트 스크립팅)](#cross-site-scripting-xss) 취약점을 도입하지 않고 데이터를 전달하는 것을 처리해야 합니다. 예를 들어 요소의 속성을 설정하여 사용자가 제공한 데이터를 DOM(문서 `innerHTML` 개체 모델)에 쓰지 마십시오. [CSP(콘텐츠 보안 정책)를](https://developer.mozilla.org/docs/Web/HTTP/CSP) `eval` 사용하여 안전하지 않은 JavaScript 프리미티브를 사용하지 않도록 설정하는 것이 좋습니다.
* 프레임워크의 디스패치 구현 위에 .NET 호출의 사용자 지정 디스패치를 구현하지 마십시오. .NET 메서드를 브라우저에 노출하는 것은 일반적인 Blazor 개발에권장되지 않는 고급 시나리오입니다.

### <a name="events"></a>이벤트

이벤트는 Blazor 서버 앱에 대한 진입점을 제공합니다. 웹 앱의 엔드포인트 보호에 대한 동일한 규칙이 Blazor Server 앱의 이벤트 처리에 적용됩니다. 악의적인 클라이언트는 이벤트의 페이로드로 전송하려는 모든 데이터를 보낼 수 있습니다.

다음은 그 예입니다.

* 에 `<select>` 대한 변경 이벤트는 앱이 클라이언트에 제공하는 옵션 내에 없는 값을 보낼 수 있습니다.
* A는 `<input>` 클라이언트 측 유효성 검사를 우회하여 서버에 텍스트 데이터를 보낼 수 있습니다.

앱은 앱이 처리하는 모든 이벤트에 대한 데이터의 유효성을 검사해야 합니다. Blazor 프레임워크는 [구성 요소가](xref:blazor/forms-validation) 기본 유효성 검사를 수행합니다. 앱에서 사용자 지정 양식 구성 요소를 사용하는 경우 이벤트 데이터의 유효성을 검사하기 위해 사용자 지정 코드를 작성해야 합니다.

Blazor서버 이벤트는 비동기이므로 앱이 새 렌더링을 생성하여 반응할 시간이 있기 전에 여러 이벤트를 서버로 디스패치할 수 있습니다. 이는 고려해야 할 몇 가지 보안 의미가 있습니다. 앱에서 클라이언트 작업을 제한하는 것은 이벤트 처리기 내에서 수행되어야 하며 현재 렌더링된 뷰 상태에 의존하지 않아야 합니다.

사용자가 카운터를 최대 3배까지 증분할 수 있도록 해야 하는 카운터 구성 요소를 고려해 보십시오. 카운터를 증분하는 버튼은 다음 의 값을 `count`기준으로 합니다.

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

클라이언트는 프레임워크가 이 구성 요소의 새 렌더링을 생성하기 전에 하나 이상의 증분 이벤트를 디스패치할 수 있습니다. 결과적으로 사용자가 단추를 `count` UI에서 충분히 빠르게 제거하지 않기 때문에 사용자가 세 번 *이상* 증분할 수 있습니다. 세 `count` 증분의 제한을 달성하는 올바른 방법은 다음 예제에 나와 있습니다.

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

처리기 `if (count < 3) { ... }` 내부에 검사를 추가하면 증분 `count` 결정은 현재 앱 상태를 기반으로 합니다. 결정은 이전 예제에서와 같이 UI의 상태를 기반으로 하지 않습니다., 일시적으로 부실 수 있습니다.

### <a name="guard-against-multiple-dispatches"></a>여러 디스패치에 대한 보호

이벤트 콜백이 외부 서비스 또는 데이터베이스에서 데이터를 가져오는 등 비동기적으로 장기 실행 작업을 호출하는 경우 guard를 사용하는 것이 좋습니다. 가드는 시각적 피드백을 통해 작업이 진행되는 동안 사용자가 여러 작업을 큐에 대기하지 못하도록 할 수 있습니다. 다음 구성 요소 `isLoading` `true` 코드는 서버에서 데이터를 가져오는 동안으로 `GetForecastAsync` 설정됩니다. `isLoading` 동안, `true`버튼은 UI에서 비활성화됩니다 .

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

배경 작업이 패턴과 비동기적으로 실행되는 경우 앞의 예제에서 보여 `async` - `await` 준 가드 패턴이 작동합니다.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>조기 취소 및 폐기 후 사용 방지

[여러 디스패치 섹션에 대해 Guard에](#guard-against-multiple-dispatches) 설명된 대로 가드를 <xref:System.Threading.CancellationToken> 사용하는 것 외에도 구성 요소를 삭제할 때 장기 실행 작업을 취소하는 것이 좋습니다. 이 방법은 구성 요소에서 *사후 배치를* 사용하지 않아도 되는 추가이점입니다.

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>많은 양의 데이터를 생성하는 이벤트 방지

또는 와 `oninput` `onscroll`같은 일부 DOM 이벤트는 많은 양의 데이터를 생성할 수 있습니다. 서버 앱에서 Blazor 이러한 이벤트를 사용하지 마십시오.

## <a name="additional-security-guidance"></a>추가 보안 지침

ASP.NET 핵심 앱 보안 지침은 Blazor 서버 앱에 적용되며 다음 섹션에서 다룹니다.

* [로깅 및 중요한 데이터](#logging-and-sensitive-data)
* [HTTPS를 통해 전송 중 정보 보호](#protect-information-in-transit-with-https)
* [크로스 사이트 스크립팅(XSS)](#cross-site-scripting-xss))
* [원산지 간 보호](#cross-origin-protection)
* [클릭 재킹](#click-jacking)
* [열기 리디렉션](#open-redirects)

### <a name="logging-and-sensitive-data"></a>로깅 및 중요한 데이터

클라이언트와 서버 간의 JS 상호 작용은 인스턴스가 있는 <xref:Microsoft.Extensions.Logging.ILogger> 서버의 로그에 기록됩니다. Blazor는 실제 이벤트 나 JS 인터옵 입력 및 출력과 같은 중요한 정보를 로깅하지 않습니다.

서버에서 오류가 발생하면 프레임워크는 클라이언트에게 통보하고 세션을 중단합니다. 기본적으로 클라이언트는 브라우저의 개발자 도구에서 볼 수 있는 일반 오류 메시지를 받습니다.

클라이언트 측 오류는 호출 스택을 포함하지 않으며 오류의 원인에 대한 세부 정보를 제공하지 않지만 서버 로그에는 이러한 정보가 포함되어 있습니다. 개발을 위해 자세한 오류를 활성화하여 클라이언트에서 중요한 오류 정보를 사용할 수 있습니다.

다음과 같은 경우 자세한 오류를 사용하도록 설정합니다.

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`구성 키를 설정합니다. 예를 들어 환경 `ASPNETCORE_DETAILEDERRORS` 변수를 `true`의 값으로 설정합니다.

> [!WARNING]
> 인터넷에서 클라이언트에 오류 정보를 노출하는 것은 항상 피해야 하는 보안 위험입니다.

### <a name="protect-information-in-transit-with-https"></a>HTTPS를 통해 전송 중 정보 보호

Blazor서버는 SignalR 클라이언트와 서버 간의 통신을 위해 사용합니다. Blazor서버는 일반적으로 WebSockets인 협상하는 SignalR 전송을 사용합니다.

Blazor서버는 서버와 클라이언트 간에 전송되는 데이터의 무결성과 기밀성을 보장하지 않습니다. 항상 HTTPS를 사용합니다.

### <a name="cross-site-scripting-xss"></a>크로스 사이트 스크립팅(XSS)

XSS(교차 사이트 스크립팅)를 사용하면 권한이 없는 당사자가 브라우저컨텍스트에서 임의로 논리를 실행할 수 있습니다. 손상된 앱은 클라이언트에서 임의의 코드를 실행할 수 있습니다. 이 취약점은 잠재적으로 서버에 대한 여러 가지 악의적인 작업을 수행하는 데 사용될 수 있습니다.

* 가짜/잘못된 이벤트를 서버에 디스패치합니다.
* 디스패치 실패/잘못된 렌더링 완료.
* 렌더링 완료를 디스패치하지 마십시오.
* 자바스크립트에서 .NET으로 인터옵 호출을 디스패치합니다.
* .NET에서 자바스크립트로 인터옵 호출의 응답을 수정합니다.
* JS 인터롭 결과에 .NET을 디스패치하지 마십시오.

Server Blazor 프레임워크는 앞의 위협 중 일부로부터 보호하기 위한 조치를 취합니다.

* 클라이언트가 렌더 일괄 처리를 승인하지 않는 경우 새 UI 업데이트 생성을 중지합니다. 로 `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`구성되었습니다.
* 클라이언트로부터 응답을 받지 않고 1분 후에 .NET을 JavaScript 호출로 시간 이동합니다. 로 `CircuitOptions.JSInteropDefaultCallTimeout`구성되었습니다.
* JS interop 동안 브라우저에서 들어오는 모든 입력에 대한 기본 유효성 검사를 수행합니다.
  * .NET 참조는 유효하며 .NET 메서드에서 예상하는 형식입니다.
  * 데이터가 잘못된 것이 아닙니다.
  * 메서드에 대 한 올바른 인수 수는 페이로드에 있습니다.
  * 인수를 호출하기 전에 인수 또는 결과를 올바르게 역직렬화할 수 있습니다.
* 디스패치 이벤트에서 브라우저에서 들어오는 모든 입력에서 기본 유효성 검사를 수행합니다.
  * 이벤트에 유효한 형식이 있습니다.
  * 이벤트에 대한 데이터는 역직렬화될 수 있습니다.
  * 이벤트와 연결된 이벤트 처리기가 있습니다.

프레임워크가 구현하는 보호 장치 외에도 위협으로부터 보호하고 적절한 조치를 취하기 위해 개발자가 앱을 코딩해야 합니다.

* 이벤트를 처리할 때 항상 데이터의 유효성을 검사합니다.
* 잘못된 데이터를 수신할 때 적절한 조치를 취하십시오.
  * 데이터를 무시하고 반환합니다. 이렇게 하면 앱에서 요청을 계속 처리할 수 있습니다.
  * 앱에서 입력이 불법이며 합법적인 클라이언트에서 생성할 수 없다고 판단되면 예외를 throw합니다. 예외를 throw하는 것은 회로를 찢어 세션을 종료합니다.
* 로그에 포함된 렌더링 일괄 처리 완료에서 제공하는 오류 메시지를 신뢰하지 마십시오. 이 오류는 클라이언트에서 제공되며 클라이언트가 손상될 수 있기 때문에 일반적으로 신뢰할 수 없습니다.
* JavaScript와 .NET 메서드 사이의 어느 방향으로든 JS interop 호출에 대한 입력을 신뢰하지 마십시오.
* 앱은 인수 또는 결과가 올바르게 역직렬화된 경우에도 인수 및 결과의 내용이 유효한지 유효성을 검사할 책임이 있습니다.

XSS 취약점이 존재하려면 앱이 렌더링된 페이지에 사용자 입력을 통합해야 합니다. Blazor서버 구성 요소는 *.razor* 파일의 태그가 절차적 C# 논리로 변환되는 컴파일 타임 단계를 실행합니다. 런타임시 C# 논리는 요소, 텍스트 및 자식 구성 요소를 설명하는 *렌더 트리를* 빌드합니다. 이것은 자바 스크립트 명령의 시퀀스를 통해 브라우저의 DOM에 적용됩니다 (또는 사전 렌더링의 경우 HTML로 직렬화됨).

* 일반 Razor 구문을 통해 렌더링된 사용자 `@someStringValue`입력(예: 텍스트만 쓸 수 있는 명령을 통해 Razor 구문이 DOM에 추가되기 때문에 XSS 취약점이 노출되지 않습니다). 값에 HTML 태그가 포함되어 있더라도 값은 정적 텍스트로 표시됩니다. 사전 렌더링할 때 출력은 HTML 인코딩되어 콘텐츠를 정적 텍스트로 표시합니다.
* 스크립트 태그는 허용되지 않으며 앱의 구성 요소 렌더 트리에 포함되어서는 안 됩니다. 스크립트 태그가 구성 요소의 태그에 포함된 경우 컴파일 타임 오류가 생성됩니다.
* 구성 요소 작성자는 Razor를 사용하지 않고 C#의 구성 요소를 작성할 수 있습니다. 구성 요소 작성자는 출력을 내보내는 경우 올바른 API를 사용할 책임이 있습니다. 예를 들어 `builder.AddContent(0, someUserSuppliedString)` XSS 취약점을 만들 수 있기 때문에 을 사용하지 *않습니다.* `builder.AddMarkupContent(0, someUserSuppliedString)`

XSS 공격으로부터 보호하는 일환으로 [CSP(콘텐츠 보안 정책)와](https://developer.mozilla.org/docs/Web/HTTP/CSP)같은 XSS 완화를 구현하는 것이 좋습니다.

자세한 내용은 <xref:security/cross-site-scripting>을 참조하세요.

### <a name="cross-origin-protection"></a>원산지 간 보호

원본 간 공격에는 다른 원본의 클라이언트가 서버에 대해 작업을 수행하는 작업이 포함됩니다. 악의적인 작업은 일반적으로 GET 요청 또는 양식 POST(교차 사이트 요청 위조, CSRF)이지만 악의적인 WebSocket을 여는 것도 가능합니다. Blazor서버 [앱은 허브 프로토콜을 SignalR 사용하는 다른 앱이 제공하는 것과 동일한 보증을 제공합니다.](xref:signalr/security)

* Blazor서버 앱을 방지하기 위한 추가 조치를 취하지 않는 한 서버 앱은 원본 간 액세스가 가능합니다. 원본 간 액세스를 사용하지 않도록 설정하려면 CORS 미들웨어를 파이프라인에 추가하고 `DisableCorsAttribute` Blazor 끝점 메타데이터에 추가하여 끝점에서 CORS를 사용하지 않도록 설정하거나 원본 간 리소스 [공유를 구성하여 SignalR ](xref:signalr/security#cross-origin-resource-sharing)허용된 원본 집합을 제한합니다.
* CORS를 사용하도록 설정한 경우 CORS 구성에 따라 앱을 보호하기 위한 추가 단계가 필요할 수 있습니다. CORS가 전역적으로 활성화된 경우 를 호출한 Blazor `DisableCorsAttribute` `hub.MapBlazorHub()`후 끝점 메타데이터에 메타데이터를 추가하여 서버 허브에 대해 CORS를 사용하지 않도록 설정할 수 있습니다.

자세한 내용은 <xref:security/anti-request-forgery>을 참조하세요.

### <a name="click-jacking"></a>클릭 재킹

클릭 재킹은 사용자가 공격을 `<iframe>` 받고 있는 사이트에서 작업을 수행하도록 속이도록 사용자를 속이기 위해 사이트를 다른 원본의 사이트 내부로 렌더링하는 것을 포함합니다.

의 내부 렌더링에서 응용 프로그램을 보호 하려면 콘텐츠 보안 정책 `X-Frame-Options` [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 및 헤더를 사용 합니다. `<iframe>` 자세한 내용은 [MDN 웹 문서: X-프레임 옵션](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)을 참조하십시오.

### <a name="open-redirects"></a>열기 리디렉션

서버 Blazor 앱 세션이 시작되면 서버는 세션 시작의 일부로 전송된 URL의 기본 유효성 검사를 수행합니다. 프레임워크는 회로를 설정하기 전에 기본 URL이 현재 URL의 부모임을 확인합니다. 프레임워크에서 추가 검사가 수행되지 않습니다.

사용자가 클라이언트에서 링크를 선택하면 링크의 URL이 서버로 전송되어 어떤 작업을 취할지 결정합니다. 예를 들어 앱은 클라이언트 쪽 탐색을 수행하거나 브라우저에 새 위치로 이동하도록 나타낼 수 있습니다.

구성 요소는 `NavigationManager`을 사용하여 탐색 요청을 프로그래밍할 수도 있습니다. 이러한 시나리오에서 앱은 클라이언트 쪽 탐색을 수행하거나 브라우저에 새 위치로 이동하도록 나타낼 수 있습니다.

구성 요소는 다음을 수행해야 합니다.

* 탐색 호출 인수의 일부로 사용자 입력을 사용하지 마십시오.
* 인수의 유효성을 검사하여 앱에서 대상이 허용되는지 확인합니다.

그렇지 않으면 악의적인 사용자가 브라우저를 공격자 제어 사이트로 이동하도록 강제할 수 있습니다. 이 시나리오에서 공격자는 `NavigationManager.Navigate` 메서드 호출의 일부로 일부 사용자 입력을 사용 하 여 응용 프로그램을 속여 합니다.

이 조언은 앱의 일부로 링크를 렌더링할 때도 적용됩니다.

* 가능하면 상대 링크를 사용합니다.
* 절대 링크 대상을 페이지에 포함하기 전에 유효한지 확인합니다.

자세한 내용은 <xref:security/preventing-open-redirects>을 참조하세요.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

인증 및 권한 부여에 <xref:security/blazor/index>대한 지침은 을 참조하십시오.

## <a name="security-checklist"></a>보안 검사 목록

다음 보안 고려 사항 목록은 포괄적이지 않습니다.

* 이벤트에서 인수를 유효성을 검사합니다.
* JS interop 호출의 입력 및 결과를 검증합니다.
* .NET에서 JS interop 호출에 대한 사용자 입력을 사용(또는 미리 확인)하지 마십시오.
* 클라이언트가 언바운드 메모리 양을 할당하지 못하도록 합니다.
  * 구성 요소 내의 데이터입니다.
  * `DotNetObject`참조가 클라이언트에 반환되었습니다.
* 여러 디스패치를 방지합니다.
* 구성 요소가 삭제될 때 장기 실행 작업을 취소합니다.
* 많은 양의 데이터를 생성하는 이벤트를 피합니다.
* 불가피한 경우 먼저 허용된 원본 집합에 대해 URL에 대한 사용자 입력을 호출의 `NavigationManager.Navigate` 일부로 사용하지 말고 사용자 입력의 유효성을 검사합니다.
* UI의 상태에 따라 권한 부여를 결정하지 말고 구성 요소 상태에서만 결정합니다.
* XSS 공격으로부터 보호하기 위해 [CSP(콘텐츠 보안 정책)를](https://developer.mozilla.org/docs/Web/HTTP/CSP) 사용하는 것이 좋습니다.
* CSP 및 [X-프레임 옵션을](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) 사용하여 클릭 재킹을 방지하십시오.
* CORS를 사용하도록 설정하거나 앱에 대해 Blazor CORS를 명시적으로 사용하지 않도록 설정할 때 CORS 설정이 적절한지 확인합니다.
* Blazor 앱의 서버 측 제한이 허용할 수 없는 수준의 위험 없이 허용 가능한 사용자 환경을 제공하는지 테스트합니다.
