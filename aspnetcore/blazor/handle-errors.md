---
title: ASP.NET Core Blazor 앱의 오류 처리
author: guardrex
description: Blazor 처리 되지 않은 예외를 관리 하는 방법 및 오류를 검색 하 고 처리 하는 앱을 개발 하는 방법을 Blazor ASP.NET Core 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: fe4cc13b1efb8c70c9632f032626aa938fb65ea3
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159952"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>ASP.NET Core Blazor 앱의 오류 처리

작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)

이 문서에서는 Blazor 처리 되지 않은 예외를 관리 하는 방법과 오류를 검색 하 고 처리 하는 앱을 개발 하는 방법을 설명 합니다

## <a name="detailed-errors-during-development"></a>개발 중의 자세한 오류

Blazor 앱이 개발 중에 올바르게 작동하지 않는 경우 앱에서 자세한 오류 정보를 수신하면 문제를 해결하고 수정하는 데 도움이 됩니다. 오류가 발생하면 Blazor 앱의 화면 아래쪽에 금색 막대가 표시됩니다.

* 개발 중에 금색 막대를 누르면 예외를 볼 수 있는 브라우저 콘솔로 연결됩니다.
* 프로덕션에서, 금색 막대는 오류가 발생했음을 알려주고 브라우저를 새로 고치도록 권장합니다.

이 오류 처리 환경의 UI는 Blazor 프로젝트 템플릿의 일부입니다. Blazor Weasembomapp에서 *wwwroot/index.html* 파일의 환경을 사용자 지정 합니다.

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Blazor Server 앱에서 *Pages/_Host* 파일의 환경을 사용자 지정 합니다.

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

`blazor-error-ui` 요소는 Blazor 템플릿에 포함 된 스타일에 의해 숨겨지고 오류가 발생할 때 표시 됩니다.

## <a name="how-the-opno-locblazor-framework-reacts-to-unhandled-exceptions"></a>Blazor 프레임 워크가 처리 되지 않은 예외에 반응 하는 방법

Blazor Server는 상태 저장 프레임 워크입니다. 사용자가 앱과 상호 작용 하는 동안 *회로*라는 서버에 대 한 연결을 유지 합니다. 회로는 활성 구성 요소 인스턴스를 비롯 하 여 상태에 대 한 다른 많은 측면을 포함 합니다.

* 구성 요소의 가장 최근 렌더링 된 출력입니다.
* 클라이언트 쪽 이벤트에 의해 트리거될 수 있는 현재 이벤트 처리 대리자 집합입니다.

사용자가 여러 브라우저 탭에서 앱을 여는 경우 여러 개의 독립적인 회로가 있습니다.

Blazor는 발생 하는 대부분의 처리 되지 않은 예외를 심각 하 게 발생 하는 회로에 처리 합니다. 처리 되지 않은 예외로 인해 회로가 종료 되는 경우 사용자는 페이지를 다시 로드 하 여 새 회로를 만들기만 하면 앱과 계속 상호 작용할 수 있습니다. 다른 사용자 또는 다른 브라우저 탭의 회로 인 종료 된 회로 외부의 회로는 영향을 받지 않습니다. 이 시나리오는 충돌 하는 앱을 다시 시작 해야 하지만 다른 앱은 영향을 받지 않는&mdash;충돌 하는 데스크톱 앱과 비슷합니다.

다음과 같은 이유로 처리 되지 않은 예외가 발생 하면 회로가 종료 됩니다.

* 처리 되지 않은 예외는 종종 회로를 정의 되지 않은 상태로 유지 합니다.
* 처리 되지 않은 예외가 발생 한 후에는 앱의 일반 작업을 보장할 수 없습니다.
* 회로를 계속 진행 하면 보안 취약성이 앱에 나타날 수 있습니다.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>개발자 코드에서 처리 되지 않은 예외 관리

오류가 발생 한 후에도 앱을 계속 하려면 앱에 오류 처리 논리가 있어야 합니다. 이 문서의 이후 섹션에서는 처리 되지 않은 예외의 잠재적 원인을 설명 합니다.

프로덕션 환경에서는 UI에서 프레임 워크 예외 메시지 또는 스택 추적을 렌더링 하지 않습니다. 예외 메시지 렌더링 또는 스택 추적:

* 최종 사용자에 게 중요 한 정보를 공개 합니다.
* 악의적인 사용자가 앱, 서버 또는 네트워크의 보안을 손상 시킬 수 있는 앱의 약점을 검색 하도록 지원 합니다.

## <a name="log-errors-with-a-persistent-provider"></a>영구 공급자를 사용 하 여 오류 기록

처리 되지 않은 예외가 발생 하는 경우 예외는 서비스 컨테이너에 구성 된 <xref:Microsoft.Extensions.Logging.ILogger> 인스턴스에 기록 됩니다. 기본적으로 Blazor 앱은 콘솔 로깅 공급자를 사용 하 여 콘솔 출력에 기록 합니다. 로그 크기와 로그 회전을 관리 하는 공급자를 사용 하 여 보다 영구적인 위치에 로깅하는 것이 좋습니다. 자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.

개발 하는 동안 Blazor는 일반적으로 디버깅에 도움이 되도록 브라우저 콘솔에 예외의 전체 세부 정보를 보냅니다. 프로덕션에서 브라우저 콘솔의 자세한 오류는 기본적으로 사용 하지 않도록 설정 되어 있습니다. 즉, 오류가 클라이언트로 전송 되지 않지만 예외의 전체 세부 정보는 여전히 서버 쪽에 기록 됩니다. 자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.

로깅할 인시던트 및 로깅 된 인시던트의 심각도 수준을 결정 해야 합니다. 악의적인 사용자가 의도적으로 오류를 트리거할 수 있습니다. 예를 들어 제품 정보를 표시 하는 구성 요소의 URL에서 알 수 없는 `ProductId` 제공 되는 오류에서 인시던트를 기록 하지 않습니다. 일부 오류는 로깅에 대 한 심각도가 높은 인시던트로 처리 되지 않습니다.

## <a name="places-where-errors-may-occur"></a>오류가 발생할 수 있는 위치

프레임 워크 및 앱 코드는 다음 위치에서 처리 되지 않은 예외를 트리거할 수 있습니다.

* [구성 요소 인스턴스화](#component-instantiation)
* [수명 주기 메서드](#lifecycle-methods)
* [렌더링 논리](#rendering-logic)
* [이벤트 처리기](#event-handlers)
* [구성 요소 삭제](#component-disposal)
* [JavaScript interop](#javascript-interop)
* [회로 처리기](#circuit-handlers)
* [회로 삭제](#circuit-disposal)
* [사전](#prerendering)

위의 처리 되지 않은 예외는이 문서의 다음 섹션에 설명 되어 있습니다.

### <a name="component-instantiation"></a>구성 요소 인스턴스화

Blazor 구성 요소의 인스턴스를 만들 때:

* 구성 요소의 생성자가 호출 됩니다.
* [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) 지시문 또는 [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) 특성을 통해 구성 요소 생성자에 제공 된 단일 항목이 아닌 DI 서비스의 생성자가 호출 됩니다. 

모든 `[Inject]` 속성에 대해 실행 된 생성자 또는 setter가 처리 되지 않은 예외를 throw 하는 경우 회로가 실패 합니다. 이 예외는 프레임 워크에서 구성 요소를 인스턴스화할 수 없기 때문에 치명적입니다. 생성자 논리에서 예외를 throw 할 수 있는 경우 앱은 오류 처리 및 로깅이 포함 된 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 하 여 예외를 트래핑 해야 합니다.

### <a name="lifecycle-methods"></a>수명 주기 메서드

구성 요소의 수명 동안 Blazor는 다음 [수명 주기 메서드](xref:blazor/lifecycle)를 호출 합니다.

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

수명 주기 메서드가 동기적 또는 비동기적으로 예외를 throw 하는 경우이는 회로에 치명적입니다. 구성 요소가 수명 주기 메서드에서 오류를 처리 하려면 오류 처리 논리를 추가 합니다.

다음 예제에서 `OnParametersSetAsync`는 메서드를 호출 하 여 제품을 가져옵니다.

* `ProductRepository.GetProductByIdAsync` 메서드에서 throw 된 예외는 `try-catch` 문에 의해 처리 됩니다.
* `catch` 블록이 실행 될 때:
  * `loadFailed`은 사용자에 게 오류 메시지를 표시 하는 데 사용 되는 `true`로 설정 됩니다.
  * 오류가 기록 됩니다.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>렌더링 논리

`.razor` 구성 요소 파일의 선언 태그는 `BuildRenderTree`라는 C# 메서드로 컴파일됩니다. 구성 요소가 렌더링 되 면 `BuildRenderTree` 실행 되 고 렌더링 된 구성 요소의 요소, 텍스트 및 자식 구성 요소를 설명 하는 데이터 구조를 작성 합니다.

렌더링 논리는 예외를 throw 할 수 있습니다. 이 시나리오의 예는 `@someObject.PropertyName`을 평가할 때 `@someObject` `null`되는 경우에 발생 합니다. 렌더링 논리에서 throw 된 처리 되지 않은 예외는 회로에 치명적입니다.

렌더링 논리에서 null 참조 예외가 발생 하지 않도록 하려면 해당 멤버에 액세스 하기 전에 `null` 개체를 확인 합니다. 다음 예에서는 `person.Address` `null`경우 `person.Address` 속성이 액세스 되지 않습니다.

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

위의 코드는 `person` `null`되지 않는다고 가정 합니다. 일반적으로 코드 구조는 구성 요소가 렌더링 될 때 개체가 존재 하는 것을 보장 합니다. 이러한 경우에는 렌더링 논리에서 `null`를 확인 하지 않아도 됩니다. 이전 예제에서는 구성 요소가 인스턴스화될 때 `person` 만들어지기 때문에 `person`가 존재 하도록 보장 될 수 있습니다.

### <a name="event-handlers"></a>이벤트 처리기

클라이언트 쪽 코드는를 사용 하 C# 여 이벤트 처리기를 만들 때 코드의 호출을 트리거합니다.

* `@onclick`
* `@onchange`
* 기타 `@on...` 특성
* `@bind`

이벤트 처리기 코드는 이러한 시나리오에서 처리 되지 않은 예외를 throw 할 수 있습니다.

이벤트 처리기가 처리 되지 않은 예외를 throw 하는 경우 (예: 데이터베이스 쿼리가 실패 하는 경우) 회로에 대 한 예외입니다. 앱에서 외부 이유로 실패할 수 있는 코드를 호출 하는 경우 오류 처리 및 로깅이 포함 된 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 하 여 예외를 트래핑 합니다.

사용자 코드에서 예외를 트래핑 및 처리 하지 않는 경우 프레임 워크는 예외를 기록 하 고 회로를 종료 합니다.

### <a name="component-disposal"></a>구성 요소 삭제

예를 들어 사용자가 다른 페이지로 이동 했으므로 UI에서 구성 요소를 제거할 수 있습니다. <xref:System.IDisposable?displayProperty=fullName>를 구현 하는 구성 요소가 UI에서 제거 되 면 프레임 워크는 구성 요소의 <xref:System.IDisposable.Dispose*> 메서드를 호출 합니다. 

구성 요소의 `Dispose` 메서드가 처리 되지 않은 예외를 throw 하는 경우 해당 예외는 회로에 치명적입니다. 삭제 논리에서 예외를 throw 할 수 있는 경우 앱은 오류 처리 및 로깅이 포함 된 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 하 여 예외를 트래핑 해야 합니다.

구성 요소 삭제에 대 한 자세한 내용은 <xref:blazor/lifecycle#component-disposal-with-idisposable>를 참조 하세요.

### <a name="javascript-interop"></a>JavaScript interop

`IJSRuntime.InvokeAsync<T>`를 사용 하면 .NET 코드에서 사용자 브라우저의 JavaScript 런타임에 대 한 비동기 호출을 수행할 수 있습니다.

다음 조건은 `InvokeAsync<T>`의 오류 처리에 적용 됩니다.

* `InvokeAsync<T>`에 대 한 호출이 동기적으로 실패 하면 .NET 예외가 발생 합니다. 예를 들어 제공 된 인수를 직렬화 할 수 없기 때문에 `InvokeAsync<T>`에 대 한 호출이 실패할 수 있습니다. 개발자 코드는 예외를 catch 해야 합니다. 이벤트 처리기 또는 구성 요소 수명 주기 메서드의 앱 코드에서 예외를 처리 하지 않으면 결과로 생성 되는 예외는 회로에 치명적입니다.
* `InvokeAsync<T>`에 대 한 호출이 비동기적으로 실패 하면 .NET <xref:System.Threading.Tasks.Task> 실패 합니다. 예를 들어 JavaScript 쪽 코드에서 예외를 throw 하거나 `rejected`으로 완료 된 `Promise`을 반환 하기 때문에 `InvokeAsync<T>`에 대 한 호출이 실패할 수 있습니다. 개발자 코드는 예외를 catch 해야 합니다. [await](/dotnet/csharp/language-reference/keywords/await) 연산자를 사용 하는 경우 오류 처리 및 로깅이 포함 된 [ try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문에 메서드 호출을 래핑하는 것이 좋습니다. 그렇지 않으면 실패 한 코드는 회로에 치명적이 지 않은 처리 되지 않은 예외를 발생 합니다.
* 기본적으로 `InvokeAsync<T>` 호출은 특정 기간 내에 완료 되어야 합니다. 그렇지 않으면 호출 시간이 초과 됩니다. 기본 제한 시간은 1 분입니다. 제한 시간은 완료 메시지를 다시 전송 하지 않는 네트워크 연결 또는 JavaScript 코드의 손실에 대해 코드를 보호 합니다. 호출 시간이 초과 되 면 결과 `Task` <xref:System.OperationCanceledException>와 함께 실패 합니다. 로깅을 사용 하 여 예외를 트래핑 하 고 처리 합니다.

마찬가지로 JavaScript 코드는 [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) 특성으로 표시 되는 .net 메서드에 대 한 호출을 시작할 수 있습니다. 이러한 .NET 메서드에서 처리 되지 않은 예외를 throw 하는 경우:

* 이 예외는 회로에 대 한 치명적으로 처리 되지 않습니다.
* JavaScript 쪽 `Promise` 거부 됩니다.

.NET 쪽 또는 메서드 호출의 JavaScript 쪽에서 오류 처리 코드를 사용 하는 옵션이 있습니다.

자세한 내용은 <xref:blazor/javascript-interop>를 참조하세요.

### <a name="circuit-handlers"></a>회로 처리기

Blazor를 사용 하 여 코드에서 사용자 회로의 상태가 변경 될 때 알림을 수신 하는 *회로 처리기*를 정의할 수 있습니다. 사용 되는 상태는 다음과 같습니다.

* `initialized`
* `connected`
* `disconnected`
* `disposed`

알림은 `CircuitHandler` 추상 기본 클래스에서 상속 되는 DI 서비스를 등록 하 여 관리 됩니다.

사용자 지정 회로 처리기의 메서드에서 처리 되지 않은 예외를 throw 하는 경우 해당 예외는 회로에 치명적입니다. 처리기의 코드 또는 호출 된 메서드에서 예외를 허용 하려면 오류 처리 및 로깅을 사용 하 여 하나 이상의 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문에 코드를 래핑합니다.

### <a name="circuit-disposal"></a>회로 삭제

사용자가 연결을 끊고 프레임 워크가 회로 상태를 정리 하기 때문에 회로가 종료 되 면 프레임 워크는 회로의 DI 범위를 삭제 합니다. 범위를 삭제 하면 <xref:System.IDisposable?displayProperty=fullName>를 구현 하는 모든 회로 범위 DI 서비스가 삭제 됩니다. 삭제 하는 동안 DI 서비스에서 처리 되지 않은 예외를 throw 하는 경우 프레임 워크는 예외를 기록 합니다.

### <a name="prerendering"></a>사전

Blazor 구성 요소는 `Component` 태그 도우미를 사용 하 여 미리 렌더링 된 수 있습니다. 이렇게 하면 렌더링 된 HTML 태그가 사용자의 초기 HTTP 요청 일부로 반환 됩니다. 다음 작업을 수행 합니다.

* 동일한 페이지의 일부인 모든 미리 렌더링 된 구성 요소에 대 한 새 회로를 만듭니다.
* 초기 HTML을 생성 합니다.
* 사용자의 브라우저가 동일한 서버에 SignalR 연결을 설정할 때까지 회로를 `disconnected`으로 처리 합니다. 연결이 설정 되 면 회로에서 상호 작용이 다시 시작 되 고 구성 요소의 HTML 태그가 업데이트 됩니다.

예를 들어, 수명 주기 방법이 나 렌더링 논리에서 렌더링 하는 동안 구성 요소가 처리 되지 않은 예외를 throw 하는 경우:

* 회로에 대 한 심각한 예외입니다.
* 예외는 `Component` 태그 도우미에서 호출 스택으로 throw 됩니다. 따라서 개발자 코드에서 예외를 명시적으로 catch 하지 않으면 전체 HTTP 요청이 실패 합니다.

일반적인 경우에는 사전 렌더링에 실패 하는 경우 작업 구성 요소를 렌더링할 수 없기 때문에 구성 요소를 계속 빌드 및 렌더링 하는 것은 적합 하지 않습니다.

렌더링 중에 발생할 수 있는 오류를 허용 하려면 예외를 throw 할 수 있는 구성 요소 내부에 오류 처리 논리를 배치 해야 합니다. 오류 처리 및 로깅과 함께 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문을 사용 합니다. `try-catch` 문에서 `Component` 태그 도우미를 래핑하는 대신 `Component` 태그 도우미에 의해 렌더링 되는 구성 요소에 오류 처리 논리를 배치 합니다.

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="recursive-rendering"></a>재귀 렌더링

구성 요소는 재귀적으로 중첩 될 수 있습니다. 이는 재귀 데이터 구조를 나타내는 데 유용 합니다. 예를 들어 `TreeNode` 구성 요소는 각 노드의 자식에 대해 더 많은 `TreeNode` 구성 요소를 렌더링할 수 있습니다.

반복적으로 렌더링 하는 경우 무한 재귀가 발생 하는 코딩 패턴을 사용 하지 않습니다.

* 주기가 포함 된 데이터 구조를 재귀적으로 렌더링 하지 않습니다. 예를 들어 자식을 포함 하는 트리 노드를 렌더링 하지 마십시오.
* 주기가 포함 된 레이아웃 체인을 만들지 않습니다. 예를 들어 레이아웃이 자체 인 레이아웃은 만들지 마십시오.
* 최종 사용자가 악의적인 데이터 입력 또는 JavaScript interop 호출을 통해 재귀 고정 (규칙)을 위반할 수 없도록 합니다.

렌더링 중 무한 루프:

* 렌더링 프로세스가 계속 지속 되도록 합니다.
* 은 종결 되지 않은 루프를 만드는 것과 같습니다.

이러한 시나리오에서 영향을 받는 회로는 중단 되 고, 스레드는 일반적으로 다음을 시도 합니다.

* 운영 체제에서 허용 하는 만큼의 CPU 시간을 무기한으로 사용 합니다.
* 서버 메모리를 무제한으로 사용 합니다. 무제한 메모리를 사용 하는 것은 종결 되지 않은 루프가 반복 될 때마다 컬렉션에 항목을 추가 하는 시나리오와 동일 합니다.

무한 재귀 패턴을 방지 하려면 재귀 렌더링 코드에 적절 한 중지 조건이 포함 되어 있는지 확인 합니다.

### <a name="custom-render-tree-logic"></a>사용자 지정 렌더링 트리 논리

대부분의 Blazor 구성 요소는 *razor* 파일로 구현 되며, 출력을 렌더링 하기 위해 `RenderTreeBuilder` 작동 하는 논리를 생성 하도록 컴파일됩니다. 개발자는 절차적 C# 코드를 사용 하 여 `RenderTreeBuilder` 논리를 수동으로 구현할 수 있습니다. 자세한 내용은 <xref:blazor/components#manual-rendertreebuilder-logic>를 참조하세요.

> [!WARNING]
> 수동 렌더링 트리 작성기 논리를 사용 하는 것은 일반적인 구성 요소 개발에는 권장 되지 않는 고급 및 안전 하지 않은 시나리오로 간주 됩니다.

`RenderTreeBuilder` 코드를 작성 하는 경우 개발자는 코드의 정확성을 보장 해야 합니다. 예를 들어 개발자는 다음을 확인 해야 합니다.

* `OpenElement` 및 `CloseElement`에 대 한 호출은 올바르게 균형이 조정 됩니다.
* 특성은 올바른 위치에만 추가 됩니다.

잘못 된 수동 렌더링 트리 작성기 논리가 있으면 충돌, 서버 중단 및 보안 취약성을 포함 하 여 정의 되지 않은 임의의 동작이 발생할 수 있습니다.

어셈블리 코드 또는 MSIL 명령을 직접 작성 하 *는 것과 동일한 수준의* 복잡성과 동일한 수준의 복잡성을 가진 수동 렌더링 트리 작성기 논리를 고려 합니다.
