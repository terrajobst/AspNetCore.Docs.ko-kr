---
title: 디버그 ASP.NET Core Blazor
author: guardrex
description: Blazor 앱을 디버그 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159991"
---
# <a name="debug-aspnet-core-opno-locblazor"></a>디버그 ASP.NET Core [!OP.NO-LOC(Blazor)]

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Chromium 기반 브라우저의 브라우저 개발 도구 (Chrome/Microsoft Edge)를 사용 하 여 [!OP.NO-LOC(Blazor)] Weasembmbs 디버깅을 위한 *초기* 지원이 있습니다. 작업이 진행 중입니다.

* Visual Studio에서 디버깅을 완전히 사용 하도록 설정 합니다.
* Visual Studio Code에서 디버깅을 사용 하도록 설정 합니다.

디버거 기능이 제한 됩니다. 사용 가능한 시나리오는 다음과 같습니다.

* 중단점을 설정 하 고 제거 합니다.
* 코드 또는 resume (`F8`) 코드 실행을 통해 단일 단계 (`F10`)를 수행 합니다.
* *지역* 표시에서 `int`, `string`및 `bool`형식의 지역 변수 값을 확인 합니다.
* JavaScript에서 .NET으로, .NET에서 JavaScript로 이동 하는 호출 체인을 포함 하 여 호출 스택을 확인 합니다.

다음을 할 *수 없습니다*.

* `int`, `string`또는 `bool`아닌 모든 지역에 대 한 값을 관찰 합니다.
* 모든 클래스 속성 또는 필드의 값을 관찰 합니다.
* 변수를 마우스로 가리켜 해당 값을 확인 합니다.
* 콘솔에서 식을 계산 합니다.
* 비동기 호출을 단계별로 실행 합니다.
* 대부분의 다른 일반적인 디버깅 시나리오를 수행 합니다.

추가 디버깅 시나리오를 개발 하는 것은 엔지니어링 팀에 집중 하는 것입니다.

## <a name="prerequisites"></a>전제 조건

디버깅 하려면 다음 브라우저 중 하나가 필요 합니다.

* Google Chrome (버전 70 이상)
* Microsoft Edge preview ([Edge Dev 채널](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>프로시저

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Visual Studio의 디버깅은 초기 개발 단계에서 지원 됩니다. **F5** 디버깅은 현재 지원 되지 않습니다.

1. 디버깅 하지 않고 `Debug` 구성에서 [!OP.NO-LOC(Blazor)] Weasembomambomsembomapp**를 실행 합니다 (** **f5**대신 **f5+f5** ).
1. 응용 프로그램의 디버그 속성 ( **디버그** 메뉴의 마지막 항목)을 열고 HTTP **앱 URL**을 복사 합니다. Chromium 기반 브라우저 (Microsoft Edge 베타 또는 Chrome)를 사용 하 여 앱의 HTTP 주소 (HTTPS 주소가 아님)로 이동 합니다.
1. 브라우저 창에서 앱에 키보드 포커스를 둡니다. 개발자 도구 패널은 포함 하지 않습니다. 이 절차에 대해서는 개발자 도구 패널을 닫은 상태로 유지 하는 것이 가장 좋습니다. 디버깅이 시작 된 후 개발자 도구 패널을 다시 열 수 있습니다.
1. 다음 [!OP.NO-LOC(Blazor)]관련 바로 가기 키를 선택 합니다.

   * Windows의 `Shift+Alt+D`
   * macOS에서 `Shift+Cmd+D`

   **디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시 되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조 하세요.
   
   원격 디버깅을 사용 하도록 설정한 후:
   
   1 \. 새 브라우저 창이 열립니다. 이전 창을 닫습니다.

   2 \. 브라우저 창에서 앱에 키보드 포커스를 놓습니다.

   3\. 새 브라우저 창에서 [!OP.NO-LOC(Blazor)]관련 바로 가기 키를 선택 합니다. Windows에서 `Shift+Alt+D` 또는 macOS에서 `Shift+Cmd+D` 합니다.

   4 \. **Devtools** 탭이 브라우저에서 열립니다. **브라우저 창에서 앱의 탭을 다시 선택 합니다.**

   앱을 Visual Studio에 연결 하려면 [Visual studio에서 프로세스에 연결](#attach-to-process-in-visual-studio) 섹션을 참조 하세요.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. `--configuration Debug` 옵션을 [dotnet run](/dotnet/core/tools/dotnet-run) 명령에 전달 하 여 `Debug` 구성에서 [!OP.NO-LOC(Blazor)] Weasembomsembomsembomsemboma 앱을 실행 합니다. `dotnet run --configuration Debug`.
1. 셸의 창에 표시 된 HTTP URL에서 앱으로 이동 합니다.
1. 개발자 도구 패널이 아닌 앱에 키보드 포커스를 둡니다. 이 절차에 대해서는 개발자 도구 패널을 닫은 상태로 유지 하는 것이 가장 좋습니다. 디버깅이 시작 된 후 개발자 도구 패널을 다시 열 수 있습니다.
1. 다음 [!OP.NO-LOC(Blazor)]관련 바로 가기 키를 선택 합니다.

   * Windows의 `Shift+Alt+D`
   * macOS에서 `Shift+Cmd+D`

   **디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시 되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조 하세요.
   
   원격 디버깅을 사용 하도록 설정한 후:
   
   1 \. 새 브라우저 창이 열립니다. 이전 창을 닫습니다.

   2 \. 브라우저 창에서 앱에 키보드 포커스를 둡니다. 개발자 도구 패널은 포함 하지 않습니다.

   3\. 새 브라우저 창에서 [!OP.NO-LOC(Blazor)]관련 바로 가기 키를 선택 합니다. Windows에서 `Shift+Alt+D` 또는 macOS에서 `Shift+Cmd+D` 합니다.

---

## <a name="enable-remote-debugging"></a>원격 디버깅 사용

원격 디버깅을 사용 하지 않도록 설정 하면 Chrome에서 디버깅 가능한 브라우저 탭 오류 페이지를 **찾을 수 없습니다** . 페이지가 생성 됩니다. 오류 페이지에는 디버깅 포트가 열려 있는 상태에서 Chrome을 실행 하는 지침이 포함 되어 Blazor 디버깅 프록시가 앱에 연결할 수 있습니다. *모든 chrome 인스턴스를 닫고* 설명 대로 chrome을 다시 시작 합니다.

## <a name="debug-the-app"></a>앱 디버그

원격 디버깅을 사용 하도록 설정 된 상태에서 Chrome을 실행 하면 디버깅 바로 가기 키가 새 디버거 탭을 엽니다. 잠시 후에 **소스** 탭에는 앱의 .net 어셈블리 목록이 표시 됩니다. 각 어셈블리를 확장 하 고 디버깅에 사용할 수 있는 *.cs*/*razor* 소스 파일을 찾습니다. 중단점을 설정 하 고, 앱의 탭으로 다시 전환 하 고, 코드가 실행 될 때 중단점이 적중 됩니다. 중단점이 적중 되 면 코드 또는 다시 시작 (`F8`) 코드를 통해 단일 단계 (`F10`)가 정상적으로 실행 됩니다.

Blazor는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/) 을 구현 하 고를 사용 하 여 프로토콜을 보강 하는 디버깅 프록시를 제공 합니다. 네트워크 관련 정보입니다. 디버그 바로 가기 키를 누르면 Blazor는 프록시의 Chrome DevTools를 가리킵니다. 프록시는 디버깅 하려는 브라우저 창에 연결 됩니다. 따라서 원격 디버깅을 사용 하도록 설정 해야 합니다.

## <a name="attach-to-process-in-visual-studio"></a>Visual Studio에서 프로세스에 연결

Visual Studio에서 앱 프로세스에 연결 하는 것은 **F5** 디버깅이 개발 중인 동안 Blazor Weasembmba에 대 한 *임시* 디버깅 시나리오입니다.

실행 중인 응용 프로그램의 프로세스를 Visual Studio에 연결 하려면 다음을 수행 합니다.

1. Visual Studio에서 **디버그** > **프로세스에 연결을**선택 합니다.
1. **연결 형식**에 대해 **Chrome devtools 프로토콜 websocket (인증 안 함)** 을 선택 합니다.
1. **연결 대상**에 대해 앱의 HTTP 주소 (HTTPS 주소가 아님)를 붙여넣습니다.
1. **사용 가능한 프로세스**에서 항목을 새로 고치려면 **새로 고침** 을 선택 합니다.
1. 디버그할 브라우저 프로세스를 선택 하 고 **연결**을 선택 합니다.
1. **코드 형식 선택** 대화 상자에서 연결 하는 특정 브라우저의 코드 형식 (가장자리 또는 Chrome)을 선택 하 고 **확인**을 선택 합니다.

## <a name="browser-source-maps"></a>브라우저 소스 맵

브라우저 소스 맵을 사용 하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑하고 클라이언트 쪽 디버깅에 일반적으로 사용 됩니다. 그러나 Blazor는 현재 JavaScript/ C# WASM에 직접 매핑되지 않습니다. 대신, Blazor는 브라우저 내에서 IL 해석을 수행 하므로 소스 맵은 관련이 없습니다.

## <a name="troubleshoot"></a>문제 해결

오류가 발생 하는 경우 다음 팁이 도움이 될 수 있습니다.

**디버거** 탭의 브라우저에서 개발자 도구를 엽니다. 콘솔에서 `localStorage.clear()`를 실행 하 여 모든 중단점을 제거 합니다.
