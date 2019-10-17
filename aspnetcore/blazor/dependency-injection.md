---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor apps에서 구성 요소에 서비스를 삽입 하는 방법을 참조 하세요.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/dependency-injection
ms.openlocfilehash: b548f0e50e1a60b74969e5bbee43860be9ba5a7f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391146"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="023ef-103">ASP.NET Core Blazor 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="023ef-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="023ef-104">[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="023ef-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="023ef-105">Blazor는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="023ef-106">앱은 기본 제공 서비스를 구성 요소에 삽입 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="023ef-107">앱은 사용자 지정 서비스를 정의 및 등록 하 고 DI를 통해 앱 전체에서 사용할 수 있게 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="023ef-108">DI는 중앙 위치에 구성 된 서비스에 액세스 하기 위한 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="023ef-109">Blazor 앱에서 다음과 같은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="023ef-110">단일 *서비스 라고 하는 여러* 구성 요소에서 서비스 클래스의 단일 인스턴스를 공유 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="023ef-111">참조 추상화를 사용 하 여 구체적인 서비스 클래스의 구성 요소를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="023ef-112">예를 들어, 응용 프로그램의 데이터에 액세스 하는 경우-0 @no__t 인터페이스를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="023ef-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="023ef-113">인터페이스는 구체적 `DataAccess` 클래스에서 구현 되 고 앱의 서비스 컨테이너에 서비스로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="023ef-114">구성 요소가 DI를 사용 하 여 `IDataAccess` 구현을 수신 하는 경우 구성 요소가 구체적인 형식에 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="023ef-115">단위 테스트에서 모의 구현을 위해 구현을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="023ef-116">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="023ef-116">Default services</span></span>

<span data-ttu-id="023ef-117">기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="023ef-118">서비스</span><span class="sxs-lookup"><span data-stu-id="023ef-118">Service</span></span> | <span data-ttu-id="023ef-119">수명</span><span class="sxs-lookup"><span data-stu-id="023ef-119">Lifetime</span></span> | <span data-ttu-id="023ef-120">설명</span><span class="sxs-lookup"><span data-stu-id="023ef-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="023ef-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="023ef-121">Singleton</span></span> | <span data-ttu-id="023ef-122">URI로 식별 되는 리소스에서 http 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="023ef-123">이 `HttpClient`의 인스턴스는 백그라운드에서 HTTP 트래픽을 처리 하기 위해 브라우저를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="023ef-124">[BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) 는 앱의 기본 URI 접두사로 자동으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="023ef-125">자세한 내용은 <xref:blazor/call-web-api>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="023ef-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="023ef-126">Singleton</span><span class="sxs-lookup"><span data-stu-id="023ef-126">Singleton</span></span> | <span data-ttu-id="023ef-127">JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="023ef-128">자세한 내용은 <xref:blazor/javascript-interop>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="023ef-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="023ef-129">Singleton</span><span class="sxs-lookup"><span data-stu-id="023ef-129">Singleton</span></span> | <span data-ttu-id="023ef-130">Uri 및 탐색 상태를 사용 하기 위한 도우미를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="023ef-131">자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/routing#uri-and-navigation-state-helpers)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="023ef-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="023ef-132">사용자 지정 서비스 공급자는 테이블에 나열 된 기본 서비스를 자동으로 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="023ef-133">사용자 지정 서비스 공급자를 사용 하 고 표에 표시 된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="023ef-134">앱에 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="023ef-134">Add services to an app</span></span>

<span data-ttu-id="023ef-135">새 앱을 만든 후 `Startup.ConfigureServices` 메서드를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="023ef-136">@No__t-0 메서드에는 서비스 설명자 개체의 목록 (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) 인 @no__t 1이 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="023ef-137">서비스 설명자를 서비스 컬렉션에 제공 하 여 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="023ef-138">다음 예제에서는 `IDataAccess` 인터페이스와 구체적인 구현 `DataAccess` 인 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="023ef-139">다음 표에 표시 된 수명을 사용 하 여 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="023ef-140">수명</span><span class="sxs-lookup"><span data-stu-id="023ef-140">Lifetime</span></span> | <span data-ttu-id="023ef-141">설명</span><span class="sxs-lookup"><span data-stu-id="023ef-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="023ef-142">Blazor Weasembomapps는 현재 DI 범위의 개념을가지고 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-142">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="023ef-143">@no__t 64, 서비스는 `Singleton` 서비스와 동일 하 게 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="023ef-144">그러나 Blazor 서버 호스팅 모델은 `Scoped` 수명을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-144">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="023ef-145">Blazor Server 앱에서 범위가 지정 된 서비스 등록 범위는 *연결*로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-145">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="023ef-146">따라서 현재 사용자로 범위를 지정 해야 하는 서비스에 대해 범위 지정 서비스를 사용 하는 것이 좋습니다. 현재 의도는 브라우저에서 클라이언트 쪽을 실행 하는 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="023ef-147">DI는 서비스의 *단일 인스턴스* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="023ef-148">@No__t-0 서비스를 필요로 하는 모든 구성 요소는 동일한 서비스의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="023ef-149">구성 요소가 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="023ef-150">DI 시스템은 ASP.NET Core에서 DI 시스템을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="023ef-151">자세한 내용은 <xref:fundamentals/dependency-injection>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="023ef-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="023ef-152">구성 요소에서 서비스 요청</span><span class="sxs-lookup"><span data-stu-id="023ef-152">Request a service in a component</span></span>

<span data-ttu-id="023ef-153">서비스 컬렉션에 서비스를 추가한 후에는 [\@inject](xref:mvc/views/razor#inject) Razor 지시어를 사용 하 여 서비스를 구성 요소에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="023ef-154">`@inject`에는 두 개의 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="023ef-155">Type &ndash; 삽입할 서비스의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="023ef-156">속성 &ndash; 삽입 된 app service를 받는 속성의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="023ef-157">속성에는 수동으로 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="023ef-158">컴파일러가 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-158">The compiler creates the property.</span></span>

<span data-ttu-id="023ef-159">자세한 내용은 <xref:mvc/views/dependency-injection>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="023ef-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="023ef-160">여러 `@inject` 문을 사용 하 여 여러 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="023ef-161">다음 예제에서는 `@inject`을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="023ef-162">@No__t-0을 구현 하는 서비스는 구성 요소의 속성 `DataRepository`에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="023ef-163">@No__t-0 추상화만 사용 하는 코드를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="023ef-164">내부적으로 생성 된 속성 (`DataRepository`)은 `InjectAttribute` 특성으로 데코 레이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="023ef-165">일반적으로이 특성은 직접 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="023ef-166">구성 요소에 기본 클래스가 필요 하 고 기본 클래스에도 삽입 된 속성이 필요 하면 `InjectAttribute`을 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="023ef-167">기본 클래스에서 파생 된 구성 요소에서는 `@inject` 지시문이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="023ef-168">기본 클래스의 `InjectAttribute` 이면 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="023ef-169">서비스에서 DI 사용</span><span class="sxs-lookup"><span data-stu-id="023ef-169">Use DI in services</span></span>

<span data-ttu-id="023ef-170">복잡 한 서비스에는 추가 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-170">Complex services might require additional services.</span></span> <span data-ttu-id="023ef-171">이전 예제에서 `DataAccess`은 @no__t 1 기본 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="023ef-172">`@inject` (또는 `InjectAttribute`)은 서비스에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="023ef-173">대신 *생성자 삽입* 을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="023ef-174">서비스의 생성자에 매개 변수를 추가 하 여 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="023ef-175">DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식 하 여 적절 하 게 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="023ef-176">생성자 삽입에 대 한 필수 조건:</span><span class="sxs-lookup"><span data-stu-id="023ef-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="023ef-177">모든 인수를 DI에서 수행할 수 있는 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="023ef-178">DI에서 다루지 않는 추가 매개 변수는 기본값을 지정 하는 경우 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="023ef-179">적용 가능한 생성자는 *public*이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="023ef-180">적용 가능한 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-180">One applicable constructor must exist.</span></span> <span data-ttu-id="023ef-181">모호성을 발생 시 DI는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="023ef-182">DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="023ef-182">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="023ef-183">ASP.NET Core 앱에서 범위가 지정 된 서비스는 일반적으로 현재 요청으로 범위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-183">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="023ef-184">요청이 완료 된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에 의해 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-184">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="023ef-185">Blazor Server 앱에서 요청 범위는 클라이언트 연결 기간 동안 지속 되므로 임시 및 범위가 지정 된 서비스가 예상 보다 훨씬 오래 지속 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-185">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="023ef-186">서비스 범위를 구성 요소의 수명으로 범위를 지정할 때는 `OwningComponentBase` 및 `OwningComponentBase<TService>` 기본 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-186">To scope services to the lifetime of a component, can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="023ef-187">이러한 기본 클래스는 구성 요소의 수명으로 범위가 지정 된 서비스를 확인 하는 `IServiceProvider` 형식의 `ScopedServices` 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-187">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="023ef-188">Razor의 기본 클래스에서 상속 되는 구성 요소를 작성 하려면 `@inherits` 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-188">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```cshtml
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
> <span data-ttu-id="023ef-189">@No__t-0 또는 `InjectAttribute`을 사용 하 여 구성 요소에 삽입 된 서비스는 구성 요소의 범위에서 만들어지지 않으며 요청 범위에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="023ef-189">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="023ef-190">추가 자료</span><span class="sxs-lookup"><span data-stu-id="023ef-190">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
