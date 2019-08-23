---
title: 디버그 ASP.NET Core Blazor
author: guardrex
description: Blazor apps를 디버그 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: blazor/debug
ms.openlocfilehash: c3188a1fe1b699b787f7a95630f3918d295d0f68
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69974904"
---
# <a name="debug-aspnet-core-blazor"></a>디버그 ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

*조기* 지원은 Blazor에서 실행 되는 클라이언트 쪽 앱을 디버그 하는 데 사용할 수 있습니다.

디버거 기능이 제한 됩니다. 사용 가능한 시나리오는 다음과 같습니다.

* 중단점을 설정 하 고 제거 합니다.
* 코드 또는 resume (`F10``F8`) 코드 실행을 통해 단일 단계 ()를 수행 합니다.
* *지역* 디스플레이에서, `int` `string`및 `bool`형식의 지역 변수 값을 관찰 합니다.
* JavaScript에서 .NET으로, .NET에서 JavaScript로 이동 하는 호출 체인을 포함 하 여 호출 스택을 확인 합니다.

다음을 할 *수 없습니다*.

* `int` ,`string`또는 가`bool`아닌 모든 지역 값을 관찰 합니다.
* 모든 클래스 속성 또는 필드의 값을 관찰 합니다.
* 변수를 마우스로 가리켜 해당 값을 확인 합니다.
* 콘솔에서 식을 계산 합니다.
* 비동기 호출을 단계별로 실행 합니다.
* 대부분의 다른 일반적인 디버깅 시나리오를 수행 합니다.

추가 디버깅 시나리오를 개발 하는 것은 엔지니어링 팀에 집중 하는 것입니다.

## <a name="prerequisites"></a>필수 구성 요소

디버깅 하려면 다음 브라우저 중 하나가 필요 합니다.

* Google Chrome (버전 70 이상)
* Microsoft Edge preview ([Edge Dev 채널](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>프로시저

1. 구성에서 `Debug` Blazor 클라이언트 쪽 앱을 실행 합니다. `--configuration Debug`옵션을 [dotnetrun](/dotnet/core/tools/dotnet-run) 명령`dotnet run --configuration Debug`에 전달합니다.
1. 브라우저에서 앱에 액세스 합니다.
1. 개발자 도구 패널이 아닌 앱에 키보드 포커스를 둡니다. 디버깅을 시작할 때 개발자 도구 패널을 닫을 수 있습니다.
1. 다음 Blazor 바로 가기 키를 선택 합니다.
   * `Shift+Alt+D`Windows/Linux에서
   * `Shift+Cmd+D`macOS
1. 화면에 나열 된 단계에 따라 원격 디버깅을 사용 하도록 설정 하 고 브라우저를 다시 시작 합니다.
1. 다음 Blazor 바로 가기 키를 다시 선택 하 여 디버그 세션을 시작 합니다.
   * `Shift+Alt+D`Windows/Linux에서
   * `Shift+Cmd+D`macOS

## <a name="enable-remote-debugging"></a>원격 디버깅 사용

원격 디버깅을 사용 하지 않도록 설정 하면 Chrome에서 디버깅 가능한 브라우저 탭 오류 페이지를 **찾을 수 없습니다** . 페이지가 생성 됩니다. 오류 페이지에는 Blazor 디버깅 프록시가 앱에 연결할 수 있도록 디버깅 포트가 열려 있는 상태로 Chrome을 실행 하기 위한 지침이 포함 되어 있습니다. *모든 chrome 인스턴스를 닫고* 설명 대로 chrome을 다시 시작 합니다.

## <a name="debug-the-app"></a>앱 디버그

원격 디버깅을 사용 하도록 설정 된 상태에서 Chrome을 실행 하면 디버깅 바로 가기 키가 새 디버거 탭을 엽니다. 잠시 후에 **소스** 탭에는 앱의 .net 어셈블리 목록이 표시 됩니다. 각 어셈블리를 확장 하 고 디버깅에 사용할 수 있는 *.cs*/*razor* 소스 파일을 찾습니다. 중단점을 설정 하 고, 앱의 탭으로 다시 전환 하 고, 코드가 실행 될 때 중단점이 적중 됩니다. 중단점이 적중 되 면 코드 또는 resume (`F10``F8`) 코드를 통해 단일 단계 ()로 실행 됩니다.

Blazor는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/) 을 구현 하 고를 사용 하 여 프로토콜을 보강 하는 디버깅 프록시를 제공 합니다. 네트워크 관련 정보입니다. 디버그 바로 가기 키를 누르면 Blazor는 프록시의 Chrome DevTools를 가리킵니다. 프록시는 디버깅 하려는 브라우저 창에 연결 됩니다. 따라서 원격 디버깅을 사용 하도록 설정 해야 합니다.

## <a name="browser-source-maps"></a>브라우저 소스 맵

브라우저 소스 맵을 사용 하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑하고 클라이언트 쪽 디버깅에 일반적으로 사용 됩니다. 그러나 Blazor는 현재 JavaScript/ C# WASM에 직접 매핑되지 않습니다. 대신, Blazor는 브라우저 내에서 IL 해석을 수행 하므로 소스 맵은 관련이 없습니다.

## <a name="troubleshooting-tip"></a>문제 해결 팁

오류가 발생 하는 경우 다음 팁이 도움이 될 수 있습니다.

**디버거** 탭의 브라우저에서 개발자 도구를 엽니다. 콘솔에서를 실행 `localStorage.clear()` 하 여 모든 중단점을 제거 합니다.
