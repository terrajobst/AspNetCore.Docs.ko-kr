---
title: ASP.NET Core Blazor 상태 관리
author: guardrex
description: Blazor Server 앱에서 상태를 유지 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/state-management
ms.openlocfilehash: 2acb91785e8c5fc34070d5428b89119667945753
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168210"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET Core Blazor 상태 관리

작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 서버는 상태 저장 앱 프레임 워크입니다. 대부분의 경우 앱은 서버에 지속적으로 연결을 유지 합니다. 사용자의 상태는 *회로*의 서버 메모리에 보관 됩니다. 

사용자 회로에 대해 유지 되는 상태 예는 다음과 같습니다.

* 구성 요소 인스턴스의&mdash;계층 구조와 가장 최근 렌더링 출력의 렌더링 된 UI입니다.
* 구성 요소 인스턴스의 모든 필드와 속성 값입니다.
* 회로로 범위가 지정 된 [DI (종속성 주입)](xref:fundamentals/dependency-injection) 서비스 인스턴스에 저장 된 데이터입니다.

> [!NOTE]
> 이 문서에서는 Blazor Server 앱에서 상태 지 속성을 다룹니다. Blazor Weasembomapps는 [브라우저에서 클라이언트 쪽 상태 지 속성](#client-side-in-the-browser) 을 활용할 수 있지만이 문서의 범위를 벗어나는 사용자 지정 솔루션 또는 타사 패키지를 요구 합니다.

## <a name="blazor-circuits"></a>Blazor 회로

사용자가 임시 네트워크 연결 손실을 경험 하는 경우 Blazor는 사용자를 원래 회로에 다시 연결 하 여 앱을 계속 사용할 수 있도록 시도 합니다. 그러나 서버 메모리의 원래 회로에 사용자를 다시 연결 하는 것은 항상 가능 하지는 않습니다.

* 서버는 연결 되지 않은 회로를 계속 유지할 수 없습니다. 서버는 시간이 초과 되거나 서버의 메모리가 부족 한 경우 연결이 끊어진 회로를 해제 해야 합니다.
* 다중 서버, 부하 분산 된 배포 환경에서는 지정 된 시간에 서버 처리 요청을 사용할 수 없게 될 수 있습니다. 개별 서버는 전체 요청 볼륨을 더 이상 처리할 필요가 없을 때 실패할 수도 있고 자동으로 제거 될 수도 있습니다. 사용자가 다시 연결 하려고 할 때 원본 서버를 사용 하지 못할 수 있습니다.
* 사용자가 브라우저를 닫았다가 다시 열거나 페이지를 다시 로드할 수 있습니다. 그러면 브라우저의 메모리에 저장 된 모든 상태가 제거 됩니다. 예를 들어 JavaScript interop 호출을 통해 설정 된 값은 손실 됩니다.

사용자를 원래 회로에 다시 연결할 수 없는 경우 사용자는 빈 상태로 새 회로를 수신 합니다. 이는 데스크톱 앱을 닫았다가 다시 여는 것과 같습니다.

## <a name="preserve-state-across-circuits"></a>회로 간 상태 유지

일부 시나리오에서는 회로 간에 상태를 유지 하는 것이 좋습니다. 앱은 다음과 같은 경우 사용자에 대 한 중요 한 데이터를 유지할 수 있습니다.

* 웹 서버를 사용할 수 없게 됩니다.
* 사용자의 브라우저는 새 웹 서버를 사용 하 여 새 회로를 강제로 시작 해야 합니다.

일반적으로 회로 간에 상태를 유지 관리 하는 것은 사용자가 이미 존재 하는 데이터를 읽는 것이 아니라 데이터를 적극적으로 만드는 시나리오에 적용 됩니다.

단일 회로 이상의 상태를 유지 하려면 *데이터를 서버 메모리에 저장 하지 마세요*. 앱은 데이터를 다른 저장소 위치에 보관 해야 합니다. 상태 지 속성은&mdash;자동이 지 않습니다. 상태 저장 데이터 지 속성을 구현 하기 위해 앱을 개발할 때 단계를 수행 해야 합니다.

데이터 지 속성은 일반적으로 사용자가 소요 된 작업을 수행 하는 높은 가치의 상태에만 필요 합니다. 다음 예에서는 상태를 유지 하 여 상업적 활동에 시간 또는 지원을 저장할 수도 있습니다.

* 다단계 webform &ndash; 상태가 손실 된 경우 사용자가 다단계 프로세스의 여러 완료 된 단계에 대 한 데이터를 다시 입력 하는 데 시간이 많이 걸립니다. 이 시나리오에서는 사용자가 다단계 폼에서 다른 곳으로 이동 하 여 나중에 폼으로 돌아갈 경우 상태가 손실 됩니다.
* 쇼핑 카트 &ndash; 잠재적 수익을 나타내는 앱의 상업적으로 중요 한 구성 요소는 유지 관리할 수 있습니다. 사용자가 자신의 상태를 상실 하 고 장바구니는 나중에 사이트로 돌아올 때 더 많은 제품이 나 서비스를 구매할 수 있습니다.

일반적으로 전송 되지 않은 로그인 대화 상자에 입력 한 사용자 이름과 같이 쉽게 다시 만들어진 상태를 유지할 필요는 없습니다.

> [!IMPORTANT]
> 앱은 *앱 상태만*유지할 수 있습니다. 구성 요소 인스턴스 및 해당 렌더링 트리와 같은 Ui는 유지할 수 없습니다. 구성 요소 및 렌더링 트리는 일반적으로 serialize 할 수 없습니다. 트리 뷰에서 확장 된 노드와 같은 UI 상태와 유사한 내용을 유지 하려면 앱에 사용자 지정 코드가 있어야 동작을 serializable 앱 상태로 모델링할 수 있습니다.

## <a name="where-to-persist-state"></a>상태를 유지 하는 위치

Blazor 서버 앱에서 상태를 유지 하기 위한 세 가지 공통 위치가 있습니다. 각 방법은 서로 다른 시나리오에 가장 적합 하며, 다음과 같은 다양 한 주의 사항이 있습니다.

* [데이터베이스의 서버 쪽](#server-side-in-a-database)
* [URL](#url)
* [브라우저의 클라이언트 쪽](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>데이터베이스의 서버 쪽

영구 데이터 지 속성 또는 여러 사용자 또는 장치에 걸쳐 있어야 하는 모든 데이터의 경우에는 독립 된 서버 쪽 데이터베이스를 선택 하는 것이 가장 좋습니다. 다음 옵션을 사용할 수 있습니다.

* 관계형 SQL 데이터베이스
* 키-값 저장소
* Blob 저장소
* 테이블 저장소

데이터를 데이터베이스에 저장 한 후에는 사용자가 언제 든 지 새 회로를 시작할 수 있습니다. 사용자의 데이터는 유지 되 고 모든 새 회로에서 사용할 수 있습니다.

Azure 데이터 저장소 옵션에 대 한 자세한 내용은 [Azure Storage 설명서](/azure/storage/) 및 [azure 데이터베이스](https://azure.microsoft.com/product-categories/databases/)를 참조 하세요.

### <a name="url"></a>URL

탐색 상태를 나타내는 임시 데이터의 경우 데이터를 URL의 일부로 모델링 합니다. URL에서 모델링 된 상태의 예는 다음과 같습니다.

* 표시 된 엔터티의 ID입니다.
* 페이지 눈금의 현재 페이지 번호입니다.

브라우저의 주소 표시줄의 내용은 유지 됩니다.

* 사용자가 페이지를 수동으로 다시 로드 하는 경우입니다.
* 웹 서버를 사용할 수&mdash;없게 되 면 사용자는 다른 서버에 연결 하기 위해 페이지를 강제로 다시 로드 해야 합니다.

지시문을 사용 하 여 URL 패턴을 `@page` 정의 하는 <xref:blazor/routing>방법에 대 한 자세한 내용은을 참조 하십시오.

### <a name="client-side-in-the-browser"></a>브라우저의 클라이언트 쪽

사용자가 적극적으로 만드는 임시 데이터의 경우 일반적인 백업 저장소는 브라우저의 `localStorage` 및 `sessionStorage` 컬렉션입니다. 회로를 중단 한 경우에는 저장 된 상태를 관리 하거나 지울 필요가 없습니다 .이는 서버 쪽 저장소의 장점입니다.

> [!NOTE]
> 이 섹션의 "클라이언트 쪽"은 [Blazor Weasembom호스팅 모델이](xref:blazor/hosting-models#blazor-webassembly)아니라 브라우저의 클라이언트 쪽 시나리오를 나타냅니다. `localStorage`및 `sessionStorage` 는 Blazor weasembomapps에서 사용할 수 있지만 사용자 지정 코드를 작성 하거나 타사 패키지를 사용 하는 경우에만 사용할 수 있습니다.

`localStorage`및 `sessionStorage` 는 다음과 같습니다.

* `localStorage`사용자의 브라우저로 범위가 지정 됩니다. 사용자가 페이지를 다시 로드 하거나 브라우저를 닫고 다시 열면 상태가 지속 됩니다. 사용자가 여러 브라우저 탭을 여는 경우 상태는 탭에서 공유 됩니다. 데이터는 명시적 `localStorage` 으로 지울 때까지 유지 됩니다.
* `sessionStorage`사용자의 브라우저 탭으로 범위가 지정 됩니다. 사용자가 탭을 다시 로드 하면 상태가 지속 됩니다. 사용자가 탭 또는 브라우저를 닫으면 상태가 손실 됩니다. 사용자가 여러 브라우저 탭을 여는 경우 각 탭에는 고유한 독립 버전의 데이터가 있습니다.

일반적으로 `sessionStorage` 는를 사용 하는 것이 더 안전 합니다. `sessionStorage`사용자가 여러 탭을 열고 다음을 발견할 위험을 방지 합니다.

* 상태 저장소의 여러 탭에 있는 버그
* 탭이 다른 탭의 상태를 덮어쓸 때 혼동 되는 동작입니다.

`localStorage`앱이 브라우저를 닫고 다시 여는 동안 상태를 유지 해야 하는 경우에는를 선택 하는 것이 좋습니다.

브라우저 저장소 사용에 대 한 주의 사항:

* 서버측 데이터베이스를 사용 하는 것과 유사 하 게 데이터 로드 및 저장은 비동기식입니다.
* 서버 쪽 데이터베이스와 달리, 요청 된 페이지는 렌더링 전 단계에서 브라우저에 존재 하지 않기 때문에 렌더링 중에는 저장소를 사용할 수 없습니다.
* Blazor 서버 앱에 대해 몇 킬로바이트의 데이터를 저장 하는 것이 합리적입니다. 네트워크를 통해 데이터를 로드 하 고 저장 하기 때문에 몇 킬로바이트 이상에서 성능 영향을 고려해 야 합니다.
* 사용자가 데이터를 보거나 조작할 수 있습니다. [데이터 보호가](xref:security/data-protection/introduction) ASP.NET Core 위험을 완화할 수 있습니다.

## <a name="third-party-browser-storage-solutions"></a>타사 브라우저 저장소 솔루션

타사 NuGet 패키지는 `localStorage` 및 `sessionStorage`를 사용 하기 위한 api를 제공 합니다.

ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)를 투명 하 게 사용 하는 패키지를 선택 하는 것이 좋습니다. ASP.NET Core 데이터 보호는 저장 된 데이터를 암호화 하 고 저장 된 데이터로 변조 될 수 있는 잠재적 위험을 줄여 줍니다. JSON 직렬화 된 데이터를 일반 텍스트로 저장 하는 경우 사용자는 브라우저 개발자 도구를 사용 하 여 데이터를 확인 하 고 저장 된 데이터를 수정할 수도 있습니다. 데이터를 보호 하는 것은 본질적으로 중요 하지 않을 수 있기 때문에 항상 문제가 되지 않습니다. 예를 들어 UI 요소의 저장 된 색을 읽거나 수정 하는 경우 사용자 또는 조직에 중요 한 보안 위험이 없습니다. 사용자가 *중요 한 데이터*를 검사 하거나 조작할 수 없도록 합니다.

## <a name="protected-browser-storage-experimental-package"></a>보호 된 브라우저 저장소 실험적 패키지

`localStorage` 및 `sessionStorage`에 대한 [데이터 보호](xref:security/data-protection/introduction)를 제공 하는 NuGet 패키지의 예는 [AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)입니다.

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`현재 지원 되지 않는 실험적 패키지가 프로덕션 사용에 적합 하지 않습니다.

### <a name="installation"></a>설치

`Microsoft.AspNetCore.ProtectedBrowserStorage` 패키지를 설치 하려면:

1. Blazor Server 앱 프로젝트에서 [AspNetCore ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)에 패키지 참조를 추가 합니다.
1. 최상위 HTML (예: 기본 프로젝트 템플릿의 *Pages/_Host* 파일)에서 다음 `<script>` 태그를 추가 합니다.

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. 메서드에서를 호출 `AddProtectedBrowserStorage` 하 여 서비스 컬렉션 `localStorage` 에 `sessionStorage` 및 서비스를 추가 합니다. `Startup.ConfigureServices`

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>구성 요소 내에서 데이터 저장 및 로드

브라우저 저장소에 데이터를 로드 하거나 저장 해야 하는 구성 요소에서 [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) 를 사용 하 여 다음 중 하나의 인스턴스를 삽입 합니다.

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

사용 하려는 백업 저장소에 따라 선택이 달라 집니다. 다음 예제 `sessionStorage` 에서는가 사용 됩니다.

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

문은 구성 요소 대신 *_imports. razor* 파일에 배치할 수 있습니다. `@using` *_Imports. razor* 파일을 사용 하면 앱의 더 큰 세그먼트 또는 전체 앱에서 네임 스페이스를 사용할 수 있습니다.

프로젝트 템플릿의 `Counter` 구성 `currentCount` 요소에 값을 유지 하려면 다음을 사용 `ProtectedSessionStore.SetAsync`하도록 메서드를수정합니다.`IncrementCount`

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

더 크고 현실적인 앱에서 개별 필드의 저장은 거의 발생 하지 않습니다. 앱은 복합 상태를 포함 하는 전체 모델 개체를 저장할 가능성이 높습니다. `ProtectedSessionStore`JSON 데이터를 자동으로 직렬화 및 deserialize 합니다.

위의 코드 예제에서 데이터는 `currentCount` 사용자의 브라우저에로 `sessionStorage['count']` 저장 됩니다. 데이터는 일반 텍스트로 저장 되지 않고 ASP.NET Core의 [데이터 보호](xref:security/data-protection/introduction)를 사용 하 여 보호 됩니다. 가 브라우저의 개발자 콘솔에서 평가 `sessionStorage['count']` 되는 경우 암호화 된 데이터를 볼 수 있습니다.

사용자가 나중 `currentCount` 에 `Counter` 구성 요소로 반환 하는 경우 데이터를 복구 하려면 (완전히 새로운 회로를 포함 하는 경우 포함) `ProtectedSessionStore.GetAsync`다음을 사용 합니다.

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

구성 요소의 매개 변수가 탐색 상태를 포함 하는 `ProtectedSessionStore.GetAsync` 경우를 호출 하 고 `OnParametersSetAsync`결과를 `OnInitializedAsync`에 할당 합니다. `OnInitializedAsync`는 구성 요소가 처음 인스턴스화될 때 한 번만 호출 됩니다. `OnInitializedAsync`사용자가 동일한 페이지에 남아 있는 동안 다른 URL로 이동 하는 경우 나중에 다시 호출 되지 않습니다.

> [!WARNING]
> 이 섹션의 예제는 서버에서 렌더링을 사용 하도록 설정 하지 않은 경우에만 작동 합니다. 렌더링을 사용 하는 경우 다음과 유사한 오류가 생성 됩니다.
>
> > 지금은 JavaScript interop 호출을 실행할 수 없습니다. 구성 요소가 미리 렌더링 된 되 고 있기 때문입니다.
>
> 렌더링 기능을 사용 하지 않도록 설정 하거나 다른 코드를 추가 하 여 렌더링을 사용 합니다. 렌더링을 사용 하는 코드 작성에 대해 자세히 알아보려면 [핸들 렌더링](#handle-prerendering) 안 함 섹션을 참조 하세요.

### <a name="handle-the-loading-state"></a>로드 상태를 처리 합니다.

브라우저 저장소는 비동기 (네트워크 연결을 통해 액세스 됨) 이기 때문에 데이터를 로드 하 고 구성 요소에서 사용할 수 있을 때까지 항상 기간이 있습니다. 최상의 결과를 위해 빈 데이터 나 기본 데이터를 표시 하는 대신 로드가 진행 되는 동안 로드 상태 메시지를 렌더링 합니다.

한 가지 방법은 데이터가 `null` 아직 로드 중인지 여부를 추적 하는 것입니다. 기본 `Counter` 구성 요소에서 수는 `int`에 저장 됩니다. 형식 `currentCount` `?`()`int`에 물음표 ()를 추가 하 여 nullable로 설정 합니다.

```csharp
private int? currentCount;
```

개수 및 **증가값** 단추를 무조건 표시 하는 대신 데이터가 로드 된 경우에만 이러한 요소를 표시 하도록 선택 합니다.

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>핸들 렌더링

렌더링 하는 동안:

* 사용자의 브라우저에 대 한 대화형 연결이 존재 하지 않습니다.
* 브라우저에 JavaScript 코드를 실행할 수 있는 페이지가 아직 없습니다.

`localStorage`또는 `sessionStorage` 렌더링 중에는 사용할 수 없습니다. 구성 요소가 저장소와 상호 작용 하려고 하면 다음과 유사한 오류가 생성 됩니다.

> 지금은 JavaScript interop 호출을 실행할 수 없습니다. 구성 요소가 미리 렌더링 된 되 고 있기 때문입니다.

오류를 해결 하는 한 가지 방법은 사전 렌더링을 사용 하지 않도록 설정 하는 것입니다. 일반적으로 앱이 브라우저 기반 저장소를 많이 사용 하는 경우이 옵션을 선택 하는 것이 좋습니다. 응용 프로그램은 또는 `localStorage` `sessionStorage` 를 사용할 수 있을 때까지 유용한 콘텐츠를 다시 만들 수 없기 때문에, 렌더링은 복잡성을 더하고 앱을 활용 하지 않습니다.

렌더링을 사용 하지 않도록 설정 하려면 *Pages/_Host* 파일을 열고에 대 `Html.RenderComponentAsync<App>(RenderMode.Server)`한 호출을 변경 합니다.

렌더링은 또는 `localStorage` `sessionStorage`를 사용 하지 않는 다른 페이지에 유용할 수 있습니다. 렌더링을 사용 하도록 설정 된 상태로 유지 하려면 브라우저가 회로에 연결 될 때까지 로드 작업을 지연 시킵니다. 카운터 값을 저장 하는 예제는 다음과 같습니다.

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore
@inject IComponentContext ComponentContext

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isWaitingForConnection;

    protected override async Task OnInitializedAsync()
    {
        if (ComponentContext.IsConnected)
        {
            // It looks like the app isn't prerendering, so the data can be
            // immediately loaded from browser storage.
            await LoadStateAsync();
        }
        else
        {
            // Prerendering is in progress, so the app defers the load operation
            // until later.
            isWaitingForConnection = true;
        }
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        // By this stage, the client has connected back to the server, and
        // browser services are available. If the app didn't load the data earlier,
        // the app should do so now and then trigger a new render.
        if (firstRender && isWaitingForConnection)
        {
            isWaitingForConnection = false;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>일반 위치로 상태 유지의 비율을 유지 합니다.

많은 구성 요소가 브라우저 기반 저장소를 사용 하는 경우 상태 공급자 코드를 여러 번 다시 구현 하면 코드 중복이 생성 됩니다. 코드 중복을 방지 하는 한 가지 옵션은 상태 공급자 논리를 캡슐화 하는 *상태 제공자 부모 구성 요소* 를 만드는 것입니다. 자식 구성 요소는 상태 지 속성 메커니즘에 관계 없이 지속형 데이터를 사용할 수 있습니다.

다음 `CounterStateProvider` 구성 요소 예제에서는 카운터 데이터가 유지 됩니다.

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

구성 `CounterStateProvider` 요소는 로드가 완료 될 때까지 자식 콘텐츠를 렌더링 하지 않고 로드 단계를 처리 합니다.

`CounterStateProvider` 구성 요소를 사용 하려면 카운터 상태에 액세스 해야 하는 다른 구성 요소 주위에 구성 요소의 인스턴스를 래핑합니다. 앱의 모든 구성 요소에서 상태를 액세스할 수 있도록 하려면 `CounterStateProvider` `App` 구성 요소 (*응용 프로그램 razor*)의를 통해 `Router` 구성 요소를 래핑합니다.

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

래핑된 구성 요소는 지속형 카운터 상태를 수신 하 고 수정할 수 있습니다. 다음 `Counter` 구성 요소는 패턴을 구현 합니다.

```cshtml
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

이전 구성 요소는와 `ProtectedBrowserStorage`상호 작용 하는 데 필요 하지 않으며 "로드 중" 단계를 처리 하지도 않습니다.

앞에서 설명한 대로 미리 렌더링을 처리 `CounterStateProvider` 하려면 카운터 데이터를 사용 하는 모든 구성 요소가 자동 렌더링에서 자동으로 작동 하도록를 수정할 수 있습니다. 자세한 내용은 [렌더링 핸들 렌더링](#handle-prerendering) 섹션을 참조 하세요.

일반적으로 *상태 제공자 부모 구성 요소* 패턴을 권장 합니다.

* 다른 많은 구성 요소에서 상태를 사용 하려면
* 유지할 최상위 상태 개체가 하나만 있는 경우입니다.

여러 가지 상태 개체를 유지 하 고 다른 위치에서 개체의 다른 하위 집합을 사용 하려면 전역적으로 상태 로드 및 저장을 처리 하지 않는 것이 좋습니다.
