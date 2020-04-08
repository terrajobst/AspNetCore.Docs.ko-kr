---
title: ASP.NET Core Blazor 상태 관리
author: guardrex
description: Blazor 서버 앱에서 상태를 유지하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218871"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>ASP.NET Core Blazor 상태 관리

작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 서버는 상태 저장 앱 프레임워크입니다. 대체로 앱은 서버 연결을 지속해서 유지 관리합니다. 사용자 상태는 서버 메모리의 ‘회로’에 저장됩니다.  

사용자 회로에 대해 저장되는 상태의 예는 다음과 같습니다.

* 렌더링된 UI - 구성 요소 인스턴스의 계층 구조 및 가장 최근 렌더링 출력
* 구성 요소 인스턴스의 모든 필드 및 속성 값
* 회로로 범위가 지정된 [DI](xref:fundamentals/dependency-injection)(종속성 주입) 서비스 인스턴스에 저장된 데이터

> [!NOTE]
> 이 문서에서는 Blazor 서버 앱의 상태 지속성을 설명합니다. Blazor WebAssembly 앱은 [브라우저의 클라이언트 쪽 상태 지속성](#client-side-in-the-browser)을 활용할 수 있지만, 이 문서의 범위를 벗어난 사용자 지정 솔루션 또는 타사 패키지를 요구합니다.

## <a name="opno-locblazor-circuits"></a>Blazor 회로

사용자에게 임시 네트워크 연결 손실이 발생하는 경우, Blazor는 사용자가 앱을 계속 사용할 수 있도록 사용자를 원래 회로에 다시 연결합니다. 그러나 사용자를 서버 메모리의 원래 회로에 다시 연결할 수 없는 경우도 있습니다.

* 서버는 연결이 끊어진 회로를 계속 유지할 수 없습니다. 시간 제한 이후 또는 서버에 메모리 압력이 발생할 경우 서버는 연결이 끊어진 회로를 해제해야 합니다.
* 부하가 분산된 다중 서버 배포 환경에서는 지정된 시간에 요청을 처리하는 서버를 사용할 수 없게 되는 경우가 있습니다. 개별 서버에서 오류가 발생하거나, 전체 요청 볼륨을 처리하는 데 더는 필요하지 않은 경우 자동으로 제거될 수도 있습니다. 사용자가 다시 연결할 때 원본 서버를 사용하지 못할 수 있습니다.
* 사용자는 브라우저를 닫았다가 다시 열거나 페이지를 다시 로드할 수 있으며, 그러면 브라우저의 메모리에 저장된 모든 상태가 제거됩니다. 예를 들어 JavaScript interop 호출을 통해 설정된 값이 손실됩니다.

사용자를 원래 회로에 다시 연결할 수 없는 경우, 사용자는 빈 상태로 새 회로를 받게 됩니다. 이 경우 데스크톱 앱을 닫았다가 다시 여는 것과 같습니다.

## <a name="preserve-state-across-circuits"></a>회로 간 상태 유지

일부 시나리오에서는 회로 간에 상태를 유지하는 것이 좋습니다. 앱은 다음과 같은 경우 사용자의 중요한 데이터를 유지할 수 있습니다.

* 웹 서버를 사용할 수 없는 경우
* 사용자 브라우저에서 새 웹 서버를 사용하여 새 회로를 강제로 시작해야 하는 경우

일반적으로 회로 간 상태 유지는 사용자가 이미 존재하는 데이터를 단순히 읽는 것이 아니라 데이터를 적극적으로 만드는 시나리오에 적용됩니다.

단일 회로 이상으로 상태를 유지하려면 ‘단순히 서버 메모리에 데이터를 저장해서는 안 됩니다’.  앱이 다른 스토리지 위치에 데이터를 저장해야 합니다. 상태 지속성은 자동이 아닙니다. 상태 저장 데이터 지속성을 구현하려면 앱을 개발할 때 해당 단계를 수행해야 합니다.

데이터 지속성은 일반적으로 사용자가 특별히 노력해서 만든 높은 가치의 상태에만 필요합니다. 다음 예제에서 상태를 유지하면 상업 활동의 시간 또는 지원을 절약할 수 있습니다.

* 다단계 WebForm - 상태가 손실된 경우 사용자가 다단계 프로세스의 완료된 여러 단계에 대한 데이터를 다시 입력하려면 오랜 시간이 걸립니다. 이 시나리오에서는 사용자가 다단계 양식을 벗어났다가 나중에 양식으로 돌아올 경우 상태가 손실됩니다.
* 쇼핑 카트 - 잠재적 수익을 나타내는, 상업적으로 중요한 앱 구성 요소를 유지 관리할 수 있습니다. 상태와 쇼핑 카트가 손실된 사용자는 나중에 사이트로 돌아올 때 제품 또는 서비스를 더 적게 구매할 수도 있습니다.

일반적으로 로그인 대화 상자에 입력되었으며 제출되지 않은 사용자 이름과 같이 다시 만들기 쉬운 상태를 유지할 필요는 없습니다.

> [!IMPORTANT]
> 앱은 ‘앱 상태’만 유지할 수 있습니다.  구성 요소 인스턴스 및 렌더링 트리와 같은 UI는 유지할 수 없습니다. 구성 요소 및 렌더링 트리는 일반적으로 직렬화할 수 없습니다. 트리 뷰의 펼친 노드와 같은, UI 상태와 유사한 내용을 유지하려면 동작을 직렬화 가능한 앱 상태로 모델링하는 사용자 지정 코드가 앱에 있어야 합니다.

## <a name="where-to-persist-state"></a>상태를 유지할 위치

Blazor 서버 앱에는 상태를 유지하기 위한 세 가지 공통 위치가 있습니다. 각 방법은 서로 다른 시나리오에 가장 적합하며, 고유한 주의 사항이 있습니다.

* [서버 쪽 데이터베이스](#server-side-in-a-database)
* [URL](#url)
* [클라이언트 쪽 브라우저](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>서버 쪽 데이터베이스

영구적인 데이터 지속성이 필요하거나 데이터가 여러 사용자 또는 디바이스에 걸쳐 있어야 하는 경우 대체로 독립적인 서버 쪽 데이터베이스를 선택하는 것이 가장 좋습니다. 다음 옵션을 사용할 수 있습니다.

* 관계형 SQL 데이터베이스
* 키-값 저장소
* Blob 저장소
* 테이블 저장소

데이터베이스에 데이터를 저장한 후에는 사용자가 언제든지 새 회로를 시작할 수 있습니다. 사용자 데이터가 보존되고 모든 새 회로에서 사용할 수 있습니다.

Azure 데이터 스토리지 옵션에 대한 자세한 내용은 [Azure Storage 설명서](/azure/storage/) 및 [Azure 데이터베이스](https://azure.microsoft.com/product-categories/databases/)를 참조하세요.

### <a name="url"></a>URL

탐색 상태를 나타내는 임시 데이터의 경우 데이터를 URL 일부로 모델링합니다. URL에 모델링된 상태의 예는 다음과 같습니다.

* 표시된 엔터티의 ID
* 페이징 그리드의 현재 페이지 번호

브라우저의 주소 표시줄 내용은 다음과 같은 경우에 유지됩니다.

* 사용자가 페이지를 수동으로 다시 로드하는 경우
* 웹 서버를 사용할 수 없는 경우 - 사용자는 다른 서버에 연결하기 위해 페이지를 강제로 다시 로드해야 합니다.

`@page` 지시문을 사용하여 URL 패턴을 정의하는 방법에 대한 자세한 내용은 <xref:blazor/routing>을 참조하세요.

### <a name="client-side-in-the-browser"></a>클라이언트 쪽 브라우저

사용자가 적극적으로 만드는 임시 데이터의 경우, 일반적인 백업 저장소는 브라우저의 `localStorage` 및 `sessionStorage` 컬렉션입니다. 회로가 중단된 경우 앱에서 저장된 상태를 관리하거나 지울 필요가 없으므로 서버 쪽 스토리지보다 이점이 있습니다.

> [!NOTE]
> 이 섹션에서 “클라이언트 쪽”은 [Blazor WebAssembly 호스팅 모델](xref:blazor/hosting-models#blazor-webassembly)이 아니라 브라우저의 클라이언트 쪽 시나리오를 가리킵니다. Blazor WebAssembly 앱에서는 `localStorage` 및 `sessionStorage`를 사용할 수 있지만, 사용자 지정 코드를 작성하거나 타사 패키지를 사용해야 합니다.

`localStorage` 및 `sessionStorage`는 다음과 같은 차이점이 있습니다.

* `localStorage`는 사용자 브라우저로 범위가 지정됩니다. 사용자가 페이지를 다시 로드하거나 브라우저를 닫았다가 다시 열면 상태가 유지됩니다. 사용자가 여러 개의 브라우저 탭을 여는 경우 탭 간에 상태가 공유됩니다. 명시적으로 지울 때까지 데이터가 `localStorage`에 유지됩니다.
* `sessionStorage`는 사용자의 브라우저 탭으로 범위가 지정됩니다. 사용자가 탭을 다시 로드하면 상태가 유지됩니다. 사용자가 탭 또는 브라우저를 닫으면 상태가 손실됩니다. 사용자가 여러 개의 브라우저 탭을 여는 경우 각 탭에 독립적인 고유한 버전의 데이터가 있습니다.

일반적으로 `sessionStorage`를 사용하는 것이 더 안전합니다. `sessionStorage`를 사용하면 사용자가 여러 탭을 열 때 다음과 같은 경우가 발생하는 위험을 방지할 수 있습니다.

* 탭 간에 상태 스토리지의 버그
* 한 탭에서 다른 탭의 상태를 덮어쓸 때 혼동되는 동작

앱이 브라우저를 닫았다가 다시 열 때 상태를 유지해야 하는 경우 `localStorage`를 선택하는 것이 좋습니다.

브라우저 스토리지를 사용하는 경우의 주의 사항:

* 서버 쪽 데이터베이스를 사용하는 경우와 유사하게, 데이터 로드 및 저장은 비동기입니다.
* 서버 쪽 데이터베이스와 달리, 미리 렌더링 단계에서는 요청한 페이지가 브라우저에 없기 때문에 미리 렌더링하는 동안 스토리지를 사용할 수 없습니다.
* 몇 킬로바이트의 데이터 스토리지는 Blazor 서버 앱에 대해 유지해도 타당합니다. 몇 킬로바이트를 넘을 경우, 네트워크를 통해 데이터를 로드하고 저장하기 때문에 성능에 미치는 영향을 고려해야 합니다.
* 사용자가 데이터를 보거나 조작할 수 있습니다. ASP.NET Core [데이터 보호](xref:security/data-protection/introduction)는 위험을 완화할 수 있습니다.

## <a name="third-party-browser-storage-solutions"></a>타사 브라우저 스토리지 솔루션

타사 NuGet 패키지는 `localStorage` 및 `sessionStorage` 작업을 위한 API를 제공합니다.

ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)를 투명하게 사용하는 패키지를 선택하는 것이 좋습니다. ASP.NET Core 데이터 보호는 저장된 데이터를 암호화하고, 저장된 데이터의 잠재적 변조 위험을 줄입니다. JSON 직렬화된 데이터를 일반 텍스트로 저장한 경우, 사용자는 브라우저 개발자 도구를 사용하여 데이터를 확인할 수 있으며 저장된 데이터를 수정할 수도 있습니다. 본질적으로 데이터가 중요하지 않을 수도 있으므로 데이터 보호가 항상 문제가 되는 것은 아닙니다. 예를 들어 저장된 UI 요소 색을 읽거나 수정하는 경우 사용자 또는 조직에 중요한 보안 위험이 되지 않습니다. 사용자가 ‘중요한 데이터’를 검사하거나 변조할 수 있도록 허용하면 안 됩니다. 

## <a name="protected-browser-storage-experimental-package"></a>보호된 브라우저 스토리지 실험적 패키지

`localStorage` 및 `sessionStorage`에 대한 [데이터 보호](xref:security/data-protection/introduction) 기능을 제공하는 NuGet 패키지의 예로 [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)가 있습니다.

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`는 지원되지 않는 실험적 패키지로, 현재 프로덕션 사용에는 적합하지 않습니다.

### <a name="installation"></a>설치

`Microsoft.AspNetCore.ProtectedBrowserStorage` 패키지를 설치하려면 다음을 수행합니다.

1. Blazor 서버 앱 프로젝트에서 [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)에 패키지 참조를 추가합니다.
1. 최상위 HTML(예: 기본 프로젝트 템플릿의 *Pages/_Host.cshtml* 파일)에서 다음 `<script>` 태그를 추가합니다.

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. `Startup.ConfigureServices` 메서드에서 `AddProtectedBrowserStorage`를 호출하여 서비스 컬렉션에 `localStorage` 및 `sessionStorage` 서비스를 추가합니다.

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>구성 요소 내에서 데이터 저장 및 로드

브라우저 스토리지에 데이터를 로드하거나 저장해야 하는 구성 요소에서 [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component)를 사용하여 다음 중 하나의 인스턴스를 삽입합니다.

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

사용하려는 백업 저장소에 따라 선택이 달라집니다. 다음 예제에서는 `sessionStorage`를 사용합니다.

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

구성 요소 대신 *_Imports.razor* 파일에 `@using` 문을 배치할 수 있습니다. *_Imports.razor* 파일을 사용하면 더 큰 앱 세그먼트나 전체 앱에서 네임스페이스를 사용할 수 있게 됩니다.

프로젝트 템플릿의 `Counter` 구성 요소에 `_currentCount` 값을 유지하려면 `ProtectedSessionStore.SetAsync`를 사용하도록 `IncrementCount` 메서드를 수정합니다.

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

더 크고 현실적인 앱에서 개별 필드 스토리지는 가능성이 거의 없는 시나리오입니다. 앱에서 복합 상태를 포함하는 전체 모델 개체를 저장할 가능성이 더 큽니다. `ProtectedSessionStore`는 JSON 데이터를 자동으로 직렬화 및 역직렬화합니다.

위의 코드 예제에서 `_currentCount` 데이터는 사용자 브라우저에 `sessionStorage['count']`로 저장됩니다. 데이터는 일반 텍스트로 저장되지 않고 ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)로 보호됩니다. 브라우저의 개발자 콘솔에서 `sessionStorage['count']`를 평가하는 경우 암호화된 데이터를 확인할 수 있습니다.

사용자가 나중에 `Counter` 구성 요소로 돌아오는 경우(완전히 새로운 회로를 사용하는 경우 포함) `_currentCount` 데이터를 복구하려면 `ProtectedSessionStore.GetAsync`를 사용합니다.

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

구성 요소의 매개 변수에 탐색 상태가 포함된 경우, `ProtectedSessionStore.GetAsync`를 호출하고 결과를 `OnInitializedAsync`가 아닌 `OnParametersSetAsync`에 할당합니다. `OnInitializedAsync`는 구성 요소를 처음 인스턴스화할 때 한 번만 호출됩니다. 나중에 사용자가 동일한 페이지를 유지하면서 다른 URL로 이동하는 경우에는 `OnInitializedAsync`가 다시 호출되지 않습니다. 자세한 내용은 <xref:blazor/lifecycle>를 참조하세요.

> [!WARNING]
> 이 섹션의 예제는 서버에서 미리 렌더링을 사용하지 않는 경우에만 작동합니다. 미리 렌더링을 사용하는 경우 다음과 같은 오류가 생성됩니다.
>
> > 지금은 JavaScript interop 호출을 실행할 수 없습니다. 구성 요소를 미리 렌더링하는 중입니다.
>
> 미리 렌더링을 사용하지 않도록 설정하거나, 미리 렌더링 작업을 위한 코드를 추가합니다. 미리 렌더링 작업을 위한 코드 작성 방법에 대한 자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.

### <a name="handle-the-loading-state"></a>로드 상태 처리

브라우저 스토리지는 비동기이므로(네트워크 연결을 통해 액세스), 데이터가 로드되고 구성 요소에서 사용할 수 있을 때까지 항상 일정 기간이 있습니다. 최상의 결과를 얻으려면 빈 데이터나 기본 데이터를 표시하는 대신, 로드가 진행되는 동안 로드 상태 메시지를 렌더링합니다.

한 가지 방법은 데이터가 `null`(로드 중)인지 여부를 추적하는 것입니다. 기본 `Counter` 구성 요소에서 개수는 `int`에 저장됩니다. 형식(`int`)에 물음표(`?`)를 추가하여 `_currentCount`를 null 허용으로 설정합니다.

```csharp
private int? _currentCount;
```

개수 및 **Increment** 단추를 무조건 표시하는 대신, 데이터가 로드된 경우에만 해당 요소를 표시하도록 선택합니다.

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>미리 렌더링 처리

미리 렌더링 과정과 관련해서 다음 사항을 확인합니다.

* 사용자 브라우저에 대한 대화형 연결이 없습니다.
* 브라우저에 JavaScript 코드를 실행할 수 있는 페이지가 아직 없습니다.

미리 렌더링하는 동안 `localStorage` 또는 `sessionStorage`를 사용할 수 없습니다. 구성 요소가 스토리지와 상호 작용하려고 하면 다음과 같은 오류가 생성됩니다.

> 지금은 JavaScript interop 호출을 실행할 수 없습니다. 구성 요소를 미리 렌더링하는 중입니다.

오류를 해결하는 한 가지 방법은 미리 렌더링을 사용하지 않도록 설정하는 것입니다. 앱에서 브라우저 기반 스토리지를 많이 사용하는 경우, 일반적으로 이 옵션을 선택하는 것이 가장 좋습니다. `localStorage` 또는 `sessionStorage`를 사용할 수 있어야 앱에서 유용한 콘텐츠를 미리 렌더링할 수 있기 때문에 미리 렌더링은 복잡성만 추가하고 앱에 도움이 되지 않습니다.

미리 렌더링을 사용하지 않도록 설정하려면 *Pages/_Host.cshtml* 파일을 열고 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)의 `render-mode`를 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>로 변경합니다.

`localStorage` 또는 `sessionStorage`를 사용하지 않는 다른 페이지에는 미리 렌더링이 유용할 수 있습니다. 미리 렌더링을 사용 상태로 유지하려면 브라우저가 회로에 연결될 때까지 로드 작업을 연기합니다. 다음은 카운터 값을 저장하기 위한 예제입니다.

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>상태 유지를 공통 위치로 추출

많은 구성 요소가 브라우저 기반 스토리지를 사용하는 경우 상태 제공자 코드를 여러 번 다시 구현하면 코드 중복이 발생합니다. 코드 중복을 방지하는 한 가지 옵션은 상태 제공자 논리를 캡슐화하는 ‘상태 제공자 부모 구성 요소’를 만드는 것입니다.  자식 구성 요소는 상태 지속성 메커니즘과 관계없이 영구 데이터를 사용할 수 있습니다.

다음 `CounterStateProvider` 구성 요소 예제에서는 카운터 데이터가 유지됩니다.

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider` 구성 요소는 로드가 완료될 때까지 자식 콘텐츠를 렌더링하지 않고 로드 단계를 처리합니다.

`CounterStateProvider` 구성 요소를 사용하려면 카운터 상태에 액세스해야 하는 다른 모든 구성 요소를 이 구성 요소 인스턴스로 래핑합니다. 앱의 모든 구성 요소가 상태에 액세스할 수 있게 하려면 `App` 구성 요소(*App.razor*)의 `Router`를 `CounterStateProvider` 구성 요소로 래핑합니다.

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

래핑된 구성 요소는 영구 카운터 상태를 받고 수정할 수 있습니다. 다음 `Counter` 구성 요소는 패턴을 구현합니다.

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

위의 구성 요소는 `ProtectedBrowserStorage`와 상호 작용하는 데 필요하지 않으며, “로드” 단계를 처리하지도 않습니다.

앞에서 설명한 대로 미리 렌더링을 처리하기 위해, 카운터 데이터를 사용하는 모든 구성 요소가 자동으로 미리 렌더링을 사용하도록 `CounterStateProvider`를 수정할 수 있습니다. 자세한 내용은 [미리 렌더링 처리](#handle-prerendering) 섹션을 참조하세요.

일반적으로 다음과 같은 경우 ‘상태 제공자 부모 구성 요소’ 패턴을 사용하는 것이 좋습니다. 

* 다른 많은 구성 요소에서 상태를 사용하려는 경우
* 유지할 최상위 상태 개체가 하나뿐인 경우

여러 다른 상태 개체를 유지하고 서로 다른 위치에 있는 여러 개체 하위 집합을 사용하려면 전역적으로 상태 로드 및 저장을 처리하지 않는 것이 좋습니다.
