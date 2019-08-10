---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor apps에서 구성 요소에 서비스를 삽입 하는 방법을 참조 하세요.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/dependency-injection
ms.openlocfilehash: a9330caa81eec0910206312283b3424c70cd1289
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68948393"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor 종속성 주입

[Rainer Stropek](https://www.timecockpit.com)

Blazor는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)를 지원 합니다. 앱은 기본 제공 서비스를 구성 요소에 삽입 하 여 사용할 수 있습니다. 앱은 사용자 지정 서비스를 정의 및 등록 하 고 DI를 통해 앱 전체에서 사용할 수 있게 할 수도 있습니다.

DI는 중앙 위치에 구성 된 서비스에 액세스 하기 위한 기술입니다. Blazor 앱에서 다음과 같은 작업을 수행할 수 있습니다.

* 단일 서비스 라고 하는 여러 구성 요소에서 서비스 클래스의 단일 인스턴스를 공유 합니다.
* 참조 추상화를 사용 하 여 구체적인 서비스 클래스의 구성 요소를 분리 합니다. 예를 들어 앱의 데이터 `IDataAccess` 에 액세스 하기 위한 인터페이스를 생각해 보겠습니다. 인터페이스는 구체적 `DataAccess` 클래스에서 구현 되 고 앱의 서비스 컨테이너에 서비스로 등록 됩니다. 구성 요소가 DI를 사용 하 여 `IDataAccess` 구현을 수신 하는 경우 구성 요소가 구체적인 형식에 연결 되지 않습니다. 단위 테스트에서 모의 구현을 위해 구현을 바꿀 수 있습니다.

## <a name="default-services"></a>기본 서비스

기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가 됩니다.

| 서비스 | 수명 | 설명 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | URI로 식별 되는 리소스에서 http 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공 합니다. 이 인스턴스 `HttpClient` 는 백그라운드에서 HTTP 트래픽을 처리 하는 데 브라우저를 사용 합니다. [BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) 는 앱의 기본 URI 접두사로 자동으로 설정 됩니다. 자세한 내용은 <xref:blazor/call-web-api>을 참조하세요. |
| `IJSRuntime` | Singleton | JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다. 자세한 내용은 <xref:blazor/javascript-interop>을 참조하세요. |
| `IUriHelper` | Singleton | Uri 및 탐색 상태를 사용 하기 위한 도우미를 포함 합니다. 자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/routing#uri-and-navigation-state-helpers)를 참조 하세요. |

사용자 지정 서비스 공급자는 테이블에 나열 된 기본 서비스를 자동으로 제공 하지 않습니다. 사용자 지정 서비스 공급자를 사용 하 고 표에 표시 된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가 합니다.

## <a name="add-services-to-an-app"></a>앱에 서비스 추가

새 앱을 만든 후 메서드를 검사 `Startup.ConfigureServices` 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

메서드에는 서비스 설명자 개체 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>의 목록 (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>)이 전달 됩니다. `ConfigureServices` 서비스 설명자를 서비스 컬렉션에 제공 하 여 서비스를 추가 합니다. 다음 예제에서는 `IDataAccess` 인터페이스 및 구체적인 구현 `DataAccess`에 대 한 개념을 보여 줍니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

다음 표에 표시 된 수명을 사용 하 여 서비스를 구성할 수 있습니다.

| 수명 | Description |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor 클라이언트 쪽에는 현재 DI 범위 개념이 없습니다. `Scoped`-등록 된 서비스는 `Singleton` 서비스 처럼 작동 합니다. 그러나 서버 쪽 호스팅 모델은 `Scoped` 수명을 지원 합니다. Razor 구성 요소에서 범위가 지정 된 서비스 등록의 범위는 연결로 지정 됩니다. 따라서 현재 사용자로 범위를 지정 해야 하는 서비스에 대해 범위 지정 서비스를 사용 하는 것이 좋습니다. 현재 의도는 브라우저에서 클라이언트 쪽을 실행 하는 경우에도 마찬가지입니다. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI는 서비스의 *단일 인스턴스* 를 만듭니다. 서비스를 필요로 하 `Singleton` 는 모든 구성 요소는 동일한 서비스의 인스턴스를 수신 합니다. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | 구성 요소가 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 를 수신 합니다. |

DI 시스템은 ASP.NET Core에서 DI 시스템을 기반으로 합니다. 자세한 내용은 <xref:fundamentals/dependency-injection>을 참조하세요.

## <a name="request-a-service-in-a-component"></a>구성 요소에서 서비스 요청

서비스 컬렉션에 서비스를 추가한 후에는 [ \@주입](xref:mvc/views/razor#inject) Razor 지시어를 사용 하 여 서비스를 구성 요소에 삽입 합니다. `@inject`에는 두 개의 매개 변수가 있습니다.

* 삽입할 &ndash; 서비스의 형식을 입력 합니다.
* 속성 &ndash; 삽입 된 app service를 받는 속성의 이름입니다. 속성에는 수동으로 만들 필요가 없습니다. 컴파일러가 속성을 만듭니다.

자세한 내용은 <xref:mvc/views/dependency-injection>을 참조하세요.

여러 개의 `@inject` 문을 사용 하 여 여러 서비스를 삽입 합니다.

다음 예제에서는 `@inject`을 사용하는 방법을 보여 줍니다. 을 구현 `Services.IDataAccess` 하는 서비스는 구성 요소의 속성 `DataRepository`에 삽입 됩니다. 코드에서 추상화를 `IDataAccess` 사용 하는 방법에 유의 하세요.

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

내부적으로 생성 된 속성 (`DataRepository`)은 `InjectAttribute` 특성을 사용 하 여 데코 레이트 됩니다. 일반적으로이 특성은 직접 사용 되지 않습니다. 구성 요소에 기본 클래스가 필요 하 고 기본 클래스에도 삽입 된 속성이 필요 하면를 수동으로 추가 합니다 `InjectAttribute`.

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

기본 클래스에서 파생 된 구성 요소에서는 `@inject` 지시문이 필요 하지 않습니다. 기본 `InjectAttribute` 클래스의는 충분 합니다.

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>서비스에서 DI 사용

복잡 한 서비스에는 추가 서비스가 필요할 수 있습니다. 이전 예제 `DataAccess` 에서에는 `HttpClient` 기본 서비스가 필요할 수 있습니다. `@inject`(또는 `InjectAttribute`)를 서비스에서 사용할 수 없습니다. 대신 *생성자 삽입* 을 사용 해야 합니다. 서비스의 생성자에 매개 변수를 추가 하 여 필요한 서비스를 추가 합니다. DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식 하 여 적절 하 게 제공 합니다.

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

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
