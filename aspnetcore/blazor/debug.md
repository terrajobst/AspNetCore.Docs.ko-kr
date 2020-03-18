---
title: ASP.NET Core Blazor 디버그
author: guardrex
description: Blazor 앱을 디버그하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648315"
---
# <a name="debug-aspnet-core-blazor"></a>ASP.NET Core Blazor 디버그

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Chromium 기반 브라우저(Chrome/Edge)에서 브라우저 개발 도구를 사용하여 Blazor WebAssembly를 디버깅하도록 하는 ‘초기’ 지원이 있습니다.  개발 중인 기능은 다음과 같습니다.

* Visual Studio에서 디버깅을 완전히 사용할 수 있도록 합니다.
* Visual Studio Code에서 디버깅을 사용할 수 있도록 합니다.

디버거 기능은 제한적입니다. 사용 가능한 시나리오는 다음과 같습니다.

* 중단점을 설정하고 제거합니다.
* 코드를 한 단계씩 실행(`F10`)하거나 코드 실행을 다시 시작(`F8`)합니다.
* ‘지역’ 표시에서 `int`, `string` 및 `bool` 형식의 지역 변수 값을 확인합니다. 
* JavaScript에서 .NET으로 이동하고 .NET에서 JavaScript로 이동하는 호출 체인을 포함하여 호출 스택을 확인합니다.

다음은 수행할 수 ‘없습니다’. 

* `int`, `string` 또는 `bool`이 아닌 지역 변수의 값을 확인합니다.
* 클래스 속성 또는 필드의 값을 확인합니다.
* 변수를 마우스로 가리켜 해당 값을 확인합니다.
* 콘솔에서 식을 계산합니다.
* 비동기 호출을 한 단계씩 실행합니다.
* 대부분의 다른 일반적인 디버깅 시나리오를 수행합니다.

엔지니어링 팀은 추가 디버깅 시나리오 개발에 지속해서 주력하고 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.

* Google Chrome(버전 70 이상)
* Microsoft Edge 미리 보기([Edge Dev 채널](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>프로시저

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Visual Studio의 디버깅 지원은 초기 개발 단계에 있습니다. **F5** 디버깅은 현재 지원되지 않습니다.

1. 디버깅하지 않고 `Debug` 구성에서 Blazor WebAssembly 앱을 실행합니다(**F5** 대신 **Ctrl**+**F5** 사용).
1. 앱의 디버그 속성(**디버그** 메뉴의 마지막 항목)을 열고 HTTP **앱 URL**을 복사합니다. Chromium 기반 브라우저(Edge 베타 또는 Chrome)를 사용하여 앱의 HTTP 주소(HTTPS 주소 아님)로 이동합니다.
1. 브라우저 창에서 개발자 도구 패널이 아니라 앱에 키보드 포커스를 놓습니다. 이 절차에서는 개발자 도구 패널을 닫은 상태로 유지하는 것이 가장 좋습니다. 디버깅이 시작된 후 개발자 도구 패널을 다시 열 수 있습니다.
1. 다음과 같은 Blazor 특정 바로 가기 키를 선택합니다.

   * Windows의 경우 `Shift+Alt+D`
   * macOS의 경우 `Shift+Cmd+D`

   **디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조하세요.
   
   원격 디버깅을 사용하도록 설정한 후 다음을 수행합니다.
   
   1\. 새 브라우저 창이 열립니다. 이전 창을 닫습니다.

   2\. 브라우저 창에서 앱에 키보드 포커스를 놓습니다.

   3\. 새 브라우저 창에서 Blazor 특정 바로 가기 키(Windows의 경우 `Shift+Alt+D`, macOS의 경우 `Shift+Cmd+D`)를 선택합니다.

   4\. **DevTools** 탭이 브라우저에서 열립니다. **브라우저 창에서 앱의 탭을 다시 선택합니다.**

   앱을 Visual Studio에 연결하려면 [Visual Studio에서 프로세스에 연결](#attach-to-process-in-visual-studio) 섹션을 참조하세요.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. `--configuration Debug` 옵션을 [dotnet run](/dotnet/core/tools/dotnet-run) 명령에 전달(`dotnet run --configuration Debug`)하여 `Debug` 구성에서 Blazor WebAssembly 앱을 실행합니다.
1. 셸의 창에 표시된 HTTP URL에 있는 앱으로 이동합니다.
1. 개발자 도구 패널이 아니라 앱에 키보드 포커스를 놓습니다. 이 절차에서는 개발자 도구 패널을 닫은 상태로 유지하는 것이 가장 좋습니다. 디버깅이 시작된 후 개발자 도구 패널을 다시 열 수 있습니다.
1. 다음과 같은 Blazor 특정 바로 가기 키를 선택합니다.

   * Windows의 경우 `Shift+Alt+D`
   * macOS의 경우 `Shift+Cmd+D`

   **디버깅 가능한 브라우저 탭을 찾을 수 없음**이 표시되는 경우 [원격 디버깅 사용](#enable-remote-debugging)을 참조하세요.
   
   원격 디버깅을 사용하도록 설정한 후 다음을 수행합니다.
   
   1\. 새 브라우저 창이 열립니다. 이전 창을 닫습니다.

   2\. 브라우저 창에서 개발자 도구 패널이 아니라 앱에 키보드 포커스를 놓습니다.

   3\. 새 브라우저 창에서 Blazor 특정 바로 가기 키(Windows의 경우 `Shift+Alt+D`, macOS의 경우 `Shift+Cmd+D`)를 선택합니다.

---

## <a name="enable-remote-debugging"></a>원격 디버깅 사용

원격 디버깅을 사용하지 않도록 설정한 경우, **디버그 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 Chrome에서 생성됩니다. 오류 페이지에는 Blazor 디버깅 프록시가 앱에 연결할 수 있도록 디버깅 포트가 열린 상태로 Chrome을 실행하는 방법에 대한 지침이 포함되어 있습니다. ‘Chrome 인스턴스를 모두 닫고’, 지침에 따라 Chrome을 다시 시작합니다. 

## <a name="debug-the-app"></a>앱 디버그

원격 디버깅을 사용하도록 설정하여 Chrome을 실행한 후 디버깅 바로 가기 키를 누르면 새 디버거 탭이 열립니다. 잠시 후에 **소스** 탭에 앱의 .NET 어셈블리 목록이 표시됩니다. 각 어셈블리를 펼쳐 디버깅에 사용할 수 있는 *.cs*/ *.razor* 소스 파일을 찾습니다. 중단점을 설정하고 앱의 탭으로 다시 전환한 다음, 코드를 실행하면 중단점이 적중됩니다. 중단점이 적중되면 코드를 한 단계씩 실행(`F10`)하거나 코드 실행을 정상적으로 다시 시작(`F8`)합니다.

Blazor에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다. 디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다. 프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.

## <a name="attach-to-process-in-visual-studio"></a>Visual Studio에서 프로세스에 연결

Visual Studio에서 앱의 프로세스에 연결하는 것은 **F5** 디버깅을 개발하는 동안 Blazor WebAssembly에 ‘임시로’ 사용되는 디버깅 시나리오입니다. 

실행 중인 앱의 프로세스를 Visual Studio에 연결하려면 다음을 수행합니다.

1. Visual Studio에서 **디버그** > **프로세스에 연결**을 선택합니다.
1. **연결 형식**에서 **Chrome devtools 프로토콜 websocket**(인증 없음)을 선택합니다.
1. **연결 대상**에 앱의 HTTP 주소(HTTPS 주소 아님)를 붙여넣습니다.
1. **새로 고침**을 선택하여 **사용 가능한 프로세스**에 있는 항목을 새로 고칩니다.
1. 디버그할 브라우저 프로세스를 선택하고 **연결**을 선택합니다.
1. **코드 형식 선택** 대화 상자에서 연결할 특정 브라우저(Edge 또는 Chrome)의 코드 형식을 선택하고 **확인**을 선택합니다.

## <a name="browser-source-maps"></a>브라우저 소스 맵

브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다. 그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다. 대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.

## <a name="troubleshoot"></a>문제 해결

오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.

**디버거** 탭에서 브라우저의 개발자 도구를 엽니다. 콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.
