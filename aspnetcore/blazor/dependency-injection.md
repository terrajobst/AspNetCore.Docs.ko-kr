---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor 앱이 서비스를 구성 요소에 삽입할 수 있는 방법을 참조 하세요.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 6930d721f04fd5f7cad2ba472724497a157fda0f
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159978"
---
# <a name="aspnet-core-opno-locblazor-dependency-injection"></a>ASP.NET Core Blazor 종속성 주입

[Rainer Stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)를 지원 합니다. 앱은 기본 제공 서비스를 구성 요소에 삽입 하 여 사용할 수 있습니다. 앱은 사용자 지정 서비스를 정의 및 등록 하 고 DI를 통해 앱 전체에서 사용할 수 있게 할 수도 있습니다.

DI는 중앙 위치에 구성 된 서비스에 액세스 하기 위한 기술입니다. 이는 Blazor 앱에서 다음과 같은 경우에 유용할 수 있습니다.

* 단일 *서비스 라고 하는 여러* 구성 요소에서 서비스 클래스의 단일 인스턴스를 공유 합니다.
* 참조 추상화를 사용 하 여 구체적인 서비스 클래스의 구성 요소를 분리 합니다. 예를 들어 응용 프로그램의 데이터에 액세스 하는 `IDataAccess` 인터페이스를 살펴보겠습니다. 인터페이스는 구체적 `DataAccess` 클래스에 의해 구현 되 고 앱의 서비스 컨테이너에 서비스로 등록 됩니다. 구성 요소가 DI를 사용 하 여 `IDataAccess` 구현을 수신 하는 경우 구성 요소가 구체적인 형식에 연결 되지 않습니다. 단위 테스트에서 모의 구현을 위해 구현을 바꿀 수 있습니다.

## <a name="default-services"></a>기본 서비스

기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가 됩니다.

| 서비스 | 수명 | 설명 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | URI로 식별 되는 리소스에서 http 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공 합니다.<br><br>Blazor Weasembomapp의 `HttpClient` 인스턴스는 브라우저를 사용 하 여 백그라운드에서 HTTP 트래픽을 처리 합니다.<br><br>Blazor Server 앱에는 기본적으로 서비스로 구성 된 `HttpClient` 포함 되지 않습니다. Blazor Server 앱에 대 한 `HttpClient`를 제공 합니다.<br><br>자세한 내용은 <xref:blazor/call-web-api>를 참조하세요. |
| `IJSRuntime` | Singleton (Blazor Weasembmbsembambmbse)<br>범위가 지정 된 (Blazor Server) | JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다. 자세한 내용은 <xref:blazor/javascript-interop>를 참조하세요. |
| `NavigationManager` | Singleton (Blazor Weasembmbsembambmbse)<br>범위가 지정 된 (Blazor Server) | Uri 및 탐색 상태를 사용 하기 위한 도우미를 포함 합니다. 자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/routing#uri-and-navigation-state-helpers)를 참조 하세요. |

사용자 지정 서비스 공급자는 테이블에 나열 된 기본 서비스를 자동으로 제공 하지 않습니다. 사용자 지정 서비스 공급자를 사용 하 고 표에 표시 된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가 합니다.

## <a name="add-services-to-an-app"></a>앱에 서비스 추가

새 앱을 만든 후 `Startup.ConfigureServices` 메서드를 검사 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

`ConfigureServices` 메서드에는<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>(서비스 설명자 개체) 목록 인 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>전달 됩니다. 서비스 설명자를 서비스 컬렉션에 제공 하 여 서비스를 추가 합니다. 다음 예제에서는 `IDataAccess` 인터페이스와 해당 `DataAccess`의 구체적인 구현에 대 한 개념을 보여 줍니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

다음 표에 표시 된 수명을 사용 하 여 서비스를 구성할 수 있습니다.

| 수명 | 설명 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor Weasembomapps는 현재 DI 범위의 개념을가지고 있지 않습니다. `Scoped`등록 된 서비스는 `Singleton` 서비스 처럼 작동 합니다. 그러나 Blazor 서버 호스팅 모델은 `Scoped` 수명을 지원 합니다. Blazor Server 앱에서 범위가 지정 된 서비스 등록 범위는 *연결*로 지정 됩니다. 따라서 현재 사용자로 범위를 지정 해야 하는 서비스에 대해 범위 지정 서비스를 사용 하는 것이 좋습니다. 현재 의도는 브라우저에서 클라이언트 쪽을 실행 하는 경우에도 마찬가지입니다. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI는 서비스의 *단일 인스턴스* 를 만듭니다. `Singleton` 서비스를 필요로 하는 모든 구성 요소는 동일한 서비스의 인스턴스를 수신 합니다. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | 구성 요소가 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 를 수신 합니다. |

DI 시스템은 ASP.NET Core에서 DI 시스템을 기반으로 합니다. 자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.

## <a name="request-a-service-in-a-component"></a>구성 요소에서 서비스 요청

서비스 컬렉션에 서비스를 추가한 후에는 [\@주입](xref:mvc/views/razor#inject) Razor 지시어를 사용 하 여 서비스를 구성 요소에 삽입 합니다. `@inject`에는 다음과 같은 두 개의 매개 변수가 있습니다.

* 삽입할 서비스의 형식 &ndash;를 입력 합니다.
* 속성 &ndash; 삽입 된 app service를 받는 속성의 이름입니다. 속성에는 수동으로 만들 필요가 없습니다. 컴파일러가 속성을 만듭니다.

자세한 내용은 <xref:mvc/views/dependency-injection>를 참조하세요.

여러 `@inject` 문을 사용 하 여 여러 서비스를 삽입 합니다.

다음 예제에서는 `@inject`을 사용하는 방법을 보여 줍니다. `Services.IDataAccess` 구현 하는 서비스는 구성 요소의 속성 `DataRepository`에 삽입 됩니다. 코드가 `IDataAccess` 추상화를 사용 하는 방법에 유의 하세요.

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

내부적으로 생성 된 속성 (`DataRepository`)은 `InjectAttribute` 특성을 사용 합니다. 일반적으로이 특성은 직접 사용 되지 않습니다. 구성 요소에 기본 클래스가 필요 하 고 기본 클래스에도 삽입 된 속성이 필요 하면 `InjectAttribute`를 수동으로 추가 합니다.

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

기본 클래스에서 파생 된 구성 요소에서는 `@inject` 지시문이 필요 하지 않습니다. 기본 클래스의 `InjectAttribute` 면 충분 합니다.

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>서비스에서 DI 사용

복잡 한 서비스에는 추가 서비스가 필요할 수 있습니다. 이전 예제에서는 `DataAccess` `HttpClient` 기본 서비스를 요구할 수 있습니다. `@inject` (또는 `InjectAttribute`)를 서비스에서 사용할 수 없습니다. 대신 *생성자 삽입* 을 사용 해야 합니다. 서비스의 생성자에 매개 변수를 추가 하 여 필요한 서비스를 추가 합니다. DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식 하 여 적절 하 게 제공 합니다.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

생성자 삽입에 대 한 필수 조건:

* 모든 인수를 DI에서 수행할 수 있는 생성자가 하나 있어야 합니다. DI에서 다루지 않는 추가 매개 변수는 기본값을 지정 하는 경우 허용 됩니다.
* 적용 가능한 생성자는 *public*이어야 합니다.
* 적용 가능한 생성자가 하나 있어야 합니다. 모호성을 발생 시 DI는 예외를 throw 합니다.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스

ASP.NET Core 앱에서 범위가 지정 된 서비스는 일반적으로 현재 요청으로 범위가 지정 됩니다. 요청이 완료 된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에 의해 삭제 됩니다. Blazor Server 앱에서 요청 범위는 클라이언트 연결 기간 동안 지속 되므로 임시 및 범위가 지정 된 서비스가 예상 보다 훨씬 오래 지속 될 수 있습니다.

서비스의 범위를 구성 요소의 수명으로 범위를 지정할 수 있도록에서는 `OwningComponentBase` 및 `OwningComponentBase<TService>` 기본 클래스를 사용할 수 있습니다. 이러한 기본 클래스는 구성 요소의 수명으로 범위가 지정 된 서비스를 확인 하는 `IServiceProvider` 형식의 `ScopedServices` 속성을 노출 합니다. Razor의 기본 클래스에서 상속 되는 구성 요소를 작성 하려면 `@inherits` 지시문을 사용 합니다.

```razor
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> `@inject` 또는 `InjectAttribute`를 사용 하 여 구성 요소에 삽입 되는 서비스는 구성 요소의 범위에 생성 되지 않으며 요청 범위에 연결 됩니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
