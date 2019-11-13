---
title: ASP.NET Core SignalR의 로깅 및 진단
author: anurse
description: ASP.NET Core SignalR 앱에서 진단을 수집 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: c5bd2ac27f8ca486b0d75aed8439747f72448625
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963851"
---
# <a name="logging-and-diagnostics-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR의 로깅 및 진단

[Andrew Stanton-간호사](https://twitter.com/anurse)

이 문서에서는 문제 해결을 위해 ASP.NET Core SignalR 앱에서 진단 정보를 수집 하기 위한 지침을 제공 합니다.

## <a name="server-side-logging"></a>서버 쪽 로깅

> [!WARNING]
> 서버 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .

SignalR은 ASP.NET Core의 일부 이므로 ASP.NET Core 로깅 시스템을 사용 합니다. 기본 구성에서 SignalR는 매우 적은 정보를 기록 하지만이를 구성할 수 있습니다. ASP.NET Core 로깅 구성에 대 한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 에 대 한 설명서를 참조 하세요.

SignalR는 두 개의로 거 범주를 사용 합니다.

* 허브 프로토콜과 관련 된 로그, 허브 활성화, 메서드 호출 및 기타 허브 관련 활동에 대 한 &ndash;를 `Microsoft.AspNetCore.SignalR` 합니다.
* Websocket, 긴 폴링 및 서버에서 보낸 이벤트, 하위 수준 SignalR 인프라 등의 전송과 관련 된 로그에 대 한 &ndash;를 `Microsoft.AspNetCore.Http.Connections` 합니다.

SignalR에서 자세한 로그를 사용 하도록 설정 하려면 `Logging`의 `LogLevel` 하위 섹션에 다음 항목을 추가 하 여 위의 접두사를 *appsettings* 파일의 `Debug` 수준으로 구성 합니다.

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

`CreateWebHostBuilder` 메서드의 코드에서이를 구성할 수도 있습니다.

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

구성 시스템에 대 한 설명서를 확인 하 여 중첩 된 구성 값을 지정 하는 방법을 확인 합니다. 예를 들어 환경 변수를 사용 하는 경우 `:` 대신 두 개의 `_` 문자가 사용 됩니다 (예: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

앱에 대 한 자세한 진단을 수집할 때 `Debug` 수준을 사용 하는 것이 좋습니다. `Trace` 수준은 매우 낮은 수준의 진단을 생성 하며 앱에서 문제를 진단 하는 데 거의 필요 하지 않습니다.

## <a name="access-server-side-logs"></a>서버 쪽 로그 액세스

서버 쪽 로그에 액세스 하는 방법은를 실행 하는 환경에 따라 달라 집니다.

### <a name="as-a-console-app-outside-iis"></a>IIS 외부의 콘솔 앱으로

콘솔 응용 프로그램에서 실행 하는 경우 [콘솔로 거](xref:fundamentals/logging/index#console-provider) 를 기본적으로 사용 하도록 설정 해야 합니다. SignalR 로그가 콘솔에 표시 됩니다.

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio의 IIS Express 내에서

Visual Studio에서 로그 출력이 **출력** 창에 표시 됩니다. **웹 서버 ASP.NET Core** 드롭다운 옵션을 선택 합니다.

### <a name="azure-app-service"></a>Azure App Service

Azure App Service 포털의 **진단 로그** 섹션에서 **응용 프로그램 로깅 (파일 시스템)** 옵션을 사용 하도록 설정 하 고 `Verbose`**수준을** 구성 합니다. 로그 **스트리밍** 서비스와 App Service 파일 시스템의 로그에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure 로그 스트리밍](xref:fundamentals/logging/index#azure-log-streaming)을 참조 하세요.

### <a name="other-environments"></a>기타 환경

앱을 다른 환경 (예: Docker, Kubernetes 또는 Windows 서비스)에 배포 하는 경우 환경에 적합 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은 <xref:fundamentals/logging/index>를 참조 하세요.

## <a name="javascript-client-logging"></a>JavaScript 클라이언트 로깅

> [!WARNING]
> 클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .

JavaScript 클라이언트를 사용 하는 경우 `HubConnectionBuilder`에서 `configureLogging` 메서드를 사용 하 여 로깅 옵션을 구성할 수 있습니다.

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

로깅을 완전히 사용 하지 않도록 설정 하려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`를 지정 합니다.

다음 표에서는 JavaScript 클라이언트에서 사용할 수 있는 로그 수준을 보여 줍니다. 로그 수준을 이러한 값 중 하나로 설정 하면 해당 수준 및 테이블 위의 모든 수준에서 로깅을 사용할 수 있습니다.

| Level | 설명 |
| ----- | ----------- |
| `None` | 메시지가 기록 되지 않습니다. |
| `Critical` | 전체 앱에서 오류를 나타내는 메시지입니다. |
| `Error` | 현재 작업의 실패를 나타내는 메시지입니다. |
| `Warning` | 심각 하지 않은 문제를 나타내는 메시지입니다. |
| `Information` | 정보 메시지. |
| `Debug` | 디버깅에 유용한 진단 메시지입니다. |
| `Trace` | 특정 문제를 진단 하기 위해 설계 된 매우 자세한 진단 메시지입니다. |

자세한 정도를 구성 하면 로그가 브라우저 콘솔에 기록 되거나 NodeJS 앱의 표준 출력에 기록 됩니다.

로그를 사용자 지정 로깅 시스템으로 전송 하려는 경우 `ILogger` 인터페이스를 구현 하는 JavaScript 개체를 제공할 수 있습니다. 구현 해야 하는 유일한 방법은 이벤트의 수준 및 이벤트와 연결 된 메시지를 사용 하는 `log`입니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET 클라이언트 로깅

> [!WARNING]
> 클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다. 프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .

.NET 클라이언트에서 로그를 가져오기 위해 `HubConnectionBuilder`에서 `ConfigureLogging` 메서드를 사용할 수 있습니다. `WebHostBuilder` 및 `HostBuilder`에서 `ConfigureLogging` 메서드와 동일한 방식으로 작동 합니다. ASP.NET Core에서 사용 하는 것과 동일한 로깅 공급자를 구성할 수 있습니다. 그러나 개별 로깅 공급자에 대 한 NuGet 패키지를 수동으로 설치 하 고 사용 하도록 설정 해야 합니다.

### <a name="console-logging"></a>콘솔 로깅

콘솔 로깅을 사용 하도록 설정 하려면 [Microsoft 확장명이 console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 패키지를 추가 합니다. 그런 다음 `AddConsole` 메서드를 사용 하 여 콘솔로 거를 구성 합니다.

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>디버그 출력 창 로깅

Visual Studio의 **출력** 창으로 이동 하도록 로그를 구성할 수도 있습니다. [Microsoft Extensions. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 패키지를 설치 하 고 `AddDebug` 메서드를 사용 합니다.

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>기타 로깅 공급자

SignalR Serilog, Seq, NLog 또는 `Microsoft.Extensions.Logging`와 통합 되는 기타 로깅 시스템과 같은 다른 로깅 공급자를 지원 합니다. 로깅 시스템에서 `ILoggerProvider`제공 하는 경우 `AddProvider`에 등록할 수 있습니다.

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>컨트롤 자세한 정도

앱의 다른 위치에서 로깅하는 경우 기본 수준을 `Debug`로 변경 하는 것이 너무 자세한 것일 수 있습니다. 필터를 사용 하 여 SignalR 로그의 로깅 수준을 구성할 수 있습니다. 이 작업은 서버에서와 거의 동일한 방식으로 코드에서 수행할 수 있습니다.

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>네트워크 추적

> [!WARNING]
> 네트워크 추적에는 앱에서 보낸 모든 메시지의 전체 콘텐츠가 포함 됩니다. 프로덕션 앱에서 GitHub와 같은 공용 포럼에 원시 네트워크 추적을 게시 **하지 마세요** .

문제가 발생 하는 경우 네트워크 추적이 많은 유용한 정보를 제공 하는 경우가 있습니다. 문제 추적기에서 문제를 해결 하려는 경우 특히 유용 합니다.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Fiddler를 사용 하 여 네트워크 추적 수집 (기본 설정 옵션)

이 메서드는 모든 앱에 대해 작동 합니다.

Fiddler는 HTTP 추적을 수집 하는 매우 강력한 도구입니다. [Telerik.com/fiddler](https://www.telerik.com/fiddler)에서 설치 하 고 시작 하 여 앱을 실행 하 고 문제를 재현 합니다. Fiddler는 Windows에서 사용할 수 있으며 macOS 및 Linux에 대 한 베타 버전이 있습니다.

HTTPS를 사용 하 여 연결 하는 경우 Fiddler에서 HTTPS 트래픽을 해독할 수 있는지 확인 하는 몇 가지 추가 단계가 있습니다. 자세한 내용은 [Fiddler 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)를 참조 하세요.

추적을 수집한 후에는 메뉴 모음에서 **파일** > **저장** > **모든 세션** 을 선택 하 여 추적을 내보낼 수 있습니다.

![Fiddler에서 모든 세션 내보내기](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump를 사용 하 여 네트워크 추적 수집 (macOS 및 Linux에만 해당)

이 메서드는 모든 앱에 대해 작동 합니다.

명령 셸에서 다음 명령을 실행 하 여 tcpdump를 사용 하 여 원시 TCP 추적을 수집할 수 있습니다. 사용 권한 오류가 발생 하면 `root` 하거나 명령에 접두사를 사용 하 여 `sudo` 해야 할 수 있습니다.

```console
tcpdump -i [interface] -w trace.pcap
```

`[interface]`를 캡처할 네트워크 인터페이스로 바꿉니다. 일반적으로 `/dev/eth0` (표준 이더넷 인터페이스의 경우) 또는 `/dev/lo0` (localhost 트래픽용)와 같은 경우입니다. 자세한 내용은 호스트 시스템의 `tcpdump` 매뉴얼 페이지를 참조 하세요.

## <a name="collect-a-network-trace-in-the-browser"></a>브라우저에서 네트워크 추적 수집

이 메서드는 브라우저 기반 앱 에서만 작동 합니다.

대부분의 브라우저 개발자 도구 브라우저와 서버 간의 네트워크 활동을 캡처할 수 있는 "네트워크" 탭이 있습니다. 그러나 이러한 추적에는 WebSocket 및 서버에서 보낸 이벤트 메시지가 포함 되지 않습니다. 이러한 전송을 사용 하는 경우 Fiddler 또는 TcpDump와 같은 도구 (아래 설명 참조)를 사용 하는 것이 더 나은 방법입니다.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge 및 Internet Explorer

에 대 한 지침은 Edge 및 Internet Explorer 모두에 대해 동일 합니다.

1. F12 키를 눌러 개발자 도구 열기
2. 네트워크 탭을 클릭 합니다.
3. 페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.
4. 도구 모음에서 저장 아이콘을 클릭 하 여 추적을 "HAR" 파일로 내보냅니다.

![Microsoft Edge Dev Tools 네트워크 탭의 저장 아이콘](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. F12 키를 눌러 개발자 도구 열기
2. 네트워크 탭을 클릭 합니다.
3. 페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.
4. 요청 목록에서 아무 곳 이나 마우스 오른쪽 단추로 클릭 하 고 "콘텐츠를 사용 하 여 HAR로 저장"을 선택 합니다.

![Google Chrome Dev Tools 네트워크 탭의 "콘텐츠를 사용 하 여 HAR로 저장" 옵션](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. F12 키를 눌러 개발자 도구 열기
2. 네트워크 탭을 클릭 합니다.
3. 페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.
4. 요청 목록에서 아무 곳 이나 마우스 오른쪽 단추로 클릭 하 고 "모두 HAR로 저장"을 선택 합니다.

![Mozilla Firefox Dev Tools 네트워크 탭에서 "모든 것을 HAR로 저장" 옵션](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>진단 파일을 GitHub 문제에 연결

`.txt` 확장을 포함 하 고 문제에 끌어서 놓으면 진단 파일의 이름을 바꿔서 GitHub 문제에 연결할 수 있습니다.

> [!NOTE]
> GitHub 문제에 로그 파일 또는 네트워크 추적의 내용을 붙여 넣지 마세요. 이러한 로그와 추적은 매우 클 수 있으며 GitHub는 일반적으로이를 자릅니다.

![GitHub 문제로 로그 파일 끌기](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>추가 자료

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
