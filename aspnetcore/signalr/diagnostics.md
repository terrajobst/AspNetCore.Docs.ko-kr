---
title: 로깅 및 ASP.NET Core SignalR의 진단
author: anurse
description: ASP.NET Core SignalR 앱에서 진단 수집 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896890"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>로깅 및 ASP.NET Core SignalR의 진단

작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)

이 문서에서는 문제를 해결 하는 ASP.NET Core SignalR 앱에서 진단 수집에 대 한 지침을 제공 합니다.

## <a name="server-side-logging"></a>서버 쪽 로깅

> [!WARNING]
> 서버 쪽 로그는 앱에서 중요 한 정보를 포함할 수 있습니다. **되지** 프로덕션 앱에서 GitHub와 같은 공개 포럼에 원시 로그를 게시 합니다.

SignalR ASP.NET Core의 일부 이므로 ASP.NET Core 로깅 시스템을 사용 합니다. 기본 구성으로 아주 적은 양의 정보를 기록 하는 SignalR 하지만 구성할 수이 있습니다. 설명서를 참조 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) ASP.NET Core 로깅 구성에 대 한 세부 정보에 대 한 합니다.

SignalR 두 개의 거 범주를 사용합니다.

* `Microsoft.AspNetCore.SignalR` -허브 프로토콜과 관련 된 로그에 대 한 허브를 활성화, 메서드 및 기타 허브 관련 작업을 호출 합니다.
* `Microsoft.AspNetCore.Http.Connections` -Websocket, 긴 폴링 및 Server-Sent 이벤트 및 SignalR 늘리고 저수준 인프라와 같은 전송와 관련 된 로그에 대 한 합니다.

SignalR에서 자세한 로그를 활성화 하려면 앞에 접두사에 대 모두를 구성 합니다 `Debug` 수준에 `appsettings.json` 파일에 다음 항목을 추가 하 여는 `LogLevel` 하위 섹션 `Logging`:

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

코드에도 구성할 수 있습니다 프로그램 `CreateWebHostBuilder` 메서드:

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

중첩 된 구성 값을 지정 하는 방법을 결정 구성 시스템에 대 한 설명서를 확인 합니다. 예를 들어, 환경 변수를 사용 하는 경우 두 `_` 문자를 대신 사용 합니다 `:` (같은: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

사용 하는 것이 좋습니다는 `Debug` 자세한 앱에 대 한 진단을 수집할 때 수준입니다. `Trace` 수준 매우 낮은 수준의 진단 생성 및 앱의 문제를 진단 하는 데 필요한 경우는 거의 없습니다.

## <a name="access-server-side-logs"></a>서버 쪽 로그 액세스

서버 쪽 로그에 액세스 하는 방법을 실행 하는 환경에 따라 달라 집니다.

### <a name="as-a-console-app-outside-iis"></a>IIS 외부 콘솔 앱

콘솔 앱에서 실행 하는 경우는 [콘솔으로 거](xref:fundamentals/logging/index#console-provider) 기본적으로 사용 하도록 설정 해야 합니다. SignalR 로그는 콘솔에 표시 됩니다.

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio에서 IIS Express에서

Visual Studio에서 로그 출력을 표시 합니다 **출력** 창입니다. 선택 된 **ASP.NET Core 웹 서버** 옵션 드롭다운 합니다.

### <a name="azure-app-service"></a>Azure App Service

Azure App Service 포털의 "진단 로그" 섹션의 "응용 프로그램 로깅 (파일 시스템)" 옵션을 설정 하 고 수준을 구성 `Verbose`합니다. 로그 로그 App Service의 파일 시스템에서와 같이 "로그 스트리밍" 서비스에도 사용할 수 있어야 합니다. 자세한 내용은 설명서를 참조 하세요 [Azure 로그 스트리밍을](xref:fundamentals/logging/index#azure-log-streaming)합니다.

### <a name="other-environments"></a>다른 환경

다른 실행 중인 경우 (Docker, Kubernetes, Windows 서비스, 등) 환경에서 전체 설명서를 참조 [ASP.NET Core 로깅](xref:fundamentals/logging/index) 환경에 적합 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은 합니다.

## <a name="javascript-client-logging"></a>JavaScript 클라이언트 로그

> [!WARNING]
> 클라이언트 쪽 로그는 앱에서 중요 한 정보를 포함할 수 있습니다. **되지** 프로덕션 앱에서 GitHub와 같은 공개 포럼에 원시 로그를 게시 합니다.

JavaScript 클라이언트를 사용 하는 경우에 사용 하 여 로깅 옵션을 구성할 수 있습니다 합니다 `configureLogging` 메서드를 `HubConnectionBuilder`:

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

로깅을 완전히 비활성화시키려면 `configureLogging` 메서드에서 `signalR.LogLevel.None`을 지정하십시오.

다음 표에서 JavaScript 클라이언트에 사용할 로그 수준을 보여 줍니다. 로그 수준은 다음이 값 중 하나로 설정 로깅을 수준 및 테이블에서 위의 모든 수준이 수 있습니다.

| 수준 | 설명 |
| ----- | ----------- |
| `None` | 메시지가 기록되지 않습니다. |
| `Critical` | 전체 앱에서 발생한 오류를 나타내는 메시지입니다. |
| `Error` | 현재 작업에서 발생한 오류를 나타내는 메시지입니다. |
| `Warning` | 치명적이지 않은 문제를 나타내는 메시지입니다. |
| `Information` | 정보 메시지입니다. |
| `Debug` | 디버깅에 유용한 진단 메시지입니다. |
| `Trace` | 특정 문제를 진단하기 위해 의도된 매우 자세한 진단 메시지입니다. |

자세한 정도 구성한 후 로그 브라우저 콘솔 (또는 NodeJS 앱에서 표준 출력)에 기록 됩니다.

사용자 지정 로깅 시스템으로 로그를 전송 하려는 경우 구현 하는 JavaScript 개체 제공할 수 있습니다는 `ILogger` 인터페이스입니다. 구현 해야 하는 유일한 방법은 `log`, 사용 하는 이벤트의 수준 및 메시지 이벤트와 연결 합니다. 예를 들어:

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET 클라이언트 로그

> [!WARNING]
> 클라이언트 쪽 로그는 앱에서 중요 한 정보를 포함할 수 있습니다. **되지** 프로덕션 앱에서 GitHub와 같은 공개 포럼에 원시 로그를 게시 합니다.

.NET 클라이언트에서 로그를 가져오려면 사용할 수 있습니다 합니다 `ConfigureLogging` 메서드를 `HubConnectionBuilder`입니다. 동일한 방식으로 작동 합니다 `ConfigureLogging` 메서드를 `WebHostBuilder` 고 `HostBuilder`입니다. ASP.NET Core에서 사용 하면 동일한 로깅 공급자를 구성할 수 있습니다. 그러나 수동으로 설치 하 고 개별 로깅 공급자에 대 한 NuGet 패키지를 사용 하도록 설정 해야 할 수도 있습니다.

### <a name="console-logging"></a>콘솔 로깅

콘솔 로깅을 사용 하려면 추가 합니다 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 패키지 있습니다. 그런 다음 사용 된 `AddConsole` 콘솔으로 거를 구성 하는 방법:

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>디버그 출력 창 로깅

로 로그를 구성할 수도 있습니다는 **출력** Visual Studio의 창. 설치를 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 패키지 및 사용 된 `AddDebug` 메서드:

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>다른 로깅 공급자

SignalR Serilog, Seq, NLog 또는와 통합 되는 다른 로깅 시스템 같은 다른 로깅 공급자를 지 원하는 `Microsoft.Extensions.Logging`합니다. 로깅 시스템에서 제공 하는 경우는 `ILoggerProvider`를 사용 하 여 등록할 수 있습니다 `AddProvider`:

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>컨트롤의 자세한 정도

앱에서 다른 위치에서 기록 하는 경우 기본 수준을 변경 `Debug` 너무 자세한 정보 표시 될 수 있습니다. SignalR 로그에 대 한 로깅 수준을 구성 하는 필터를 사용할 수 있습니다. 이렇게 하려면 코드의 대부분 동일한 방식으로 서버에서:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>네트워크 추적

> [!WARNING]
> 네트워크 추적에는 앱에서 보낸 모든 메시지의 전체 콘텐츠를 포함 합니다. **되지** 원시 네트워크 추적을 프로덕션 앱에서 GitHub와 같은 공개 포럼에 게시 합니다.

문제가 발생 하는 경우 네트워크 추적 유용한 정보가 많은 경우에 따라 제공할 수 있습니다. 문제 추적기에서 문제를 제출 하려는 경우 특히 유용 합니다.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>(기본 옵션) Fiddler 사용 하 여 네트워크 추적을 수집 합니다.

이 메서드는 모든 앱에 대해 작동합니다.

Fiddler는 HTTP 추적을 수집 하기 위한 강력한 도구입니다. 설치할 [telerik.com/fiddler](https://www.telerik.com/fiddler), 시작 하 고 다음 앱을 실행 하 고 문제를 재현 합니다. Fiddler Windows를 사용할 수 있으며는 macOS 및 Linux에 대 한 베타 버전이 있습니다.

HTTPS를 사용 하 여 연결 하는 경우 몇 가지 추가 단계 Fiddler HTTPS 트래픽 암호를 해독할 수 있도록 합니다. 자세한 내용은 참조는 [Fiddler 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)합니다.

추적을 수집한 후 선택 하 여 추적을 내보낼 수 있습니다 **파일** > **저장** > **모든 세션...**  메뉴 모음에서.

![Fiddler에서 모든 세션 내보내기](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump (macOS 및 Linux에만 해당)를 사용 하 여 네트워크 추적을 수집 합니다.

이 메서드는 모든 앱에 대해 작동합니다.

Tcpdump 명령 셸에서 다음 명령을 실행 하 여 원시 TCP 추적을 수집할 수 있습니다. 해야 할 수 있습니다 `root` 사용 하 여 명령을 접두사 또는 `sudo` 권한 오류가 발생할 경우:

```console
tcpdump -i [interface] -w trace.pcap
```

대체 `[interface]` 에서 캡처 하려는 네트워크 인터페이스를 사용 하 여 합니다. 일반적으로이 같습니다 `/dev/eth0` (표준 이더넷 인터페이스)에 대 한 또는 `/dev/lo0` (localhost 트래픽용). 자세한 내용은 참조는 `tcpdump` 호스트 시스템에서 기본 페이지입니다.

## <a name="collect-a-network-trace-in-the-browser"></a>브라우저에서 네트워크 추적을 수집 합니다.

이 메서드는 브라우저 기반 앱 에서만 작동합니다.

대부분의 브라우저 개발자 도구는 브라우저와 서버 간의 네트워크 활동을 캡처할 수 있는 "네트워크" 탭을 갖습니다. 그러나 이러한 추적 WebSocket 및 Server-Sent 이벤트 메시지를 포함 하지 않습니다. 이러한 전송을 사용 하는 경우 Fiddler 또는 TcpDump (아래 설명 참조)와 같은 도구를 사용 하는 더 나은 방법이 있습니다.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge 및 Internet Explorer

(지침에 대해 동일 Edge와 Internet Explorer)

1. F12 키를 눌러 개발자 도구 열기
2. 네트워크 탭을 클릭 합니다.
3. 문제를 재현 및 (필요한 경우) 페이지를 새로 고칩니다.
4. 추적 "HAR" 파일로 내보내려면 도구 모음에서 저장 아이콘을 클릭 합니다.

![저장 아이콘을 Microsoft Edge 개발자 도구 네트워크 탭](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. F12 키를 눌러 개발자 도구 열기
2. 네트워크 탭을 클릭 합니다.
3. 문제를 재현 및 (필요한 경우) 페이지를 새로 고칩니다.
4. 선택한 요청 목록에서 아무 곳 이나 클릭 마우스 오른쪽 단추로 "Save as HAR with 콘텐츠":

![Google Chrome 개발자 도구 네트워크 탭에서 "콘텐츠를 사용 하 여 HAR로 저장" 옵션](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. F12 키를 눌러 개발자 도구 열기
2. 네트워크 탭을 클릭 합니다.
3. 문제를 재현 및 (필요한 경우) 페이지를 새로 고칩니다.
4. "저장 모든으로 HAR" 요청 목록에서 아무 곳 이나 클릭 마우스 오른쪽 단추로 선택한

![Mozilla Firefox 개발자 도구 네트워크 탭에서 "저장 HAR로 모두" 옵션](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>GitHub 문제를 진단 파일을 첨부 합니다.

GitHub 문제에 있어 바꾸어 진단 파일을 첨부할 수 있습니다를 `.txt` 확장 하 고 다음 끌어서 놓아 문제에 로그온 합니다.

> [!NOTE]
> GitHub 문제에서 네트워크 추적 또는 로그 파일의 내용을 붙여넣으세요 하지 마십시오. 이러한 로그 및 추적 매우 클 수 있으며 GitHub는 일반적으로 잘라내려면 합니다.

![GitHub 문제에 대 한 로그 파일을 끌어](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>추가 자료

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
