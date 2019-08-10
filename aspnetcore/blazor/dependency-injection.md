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
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="bcba4-103">ASP.NET Core Blazor 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="bcba4-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="bcba4-104">[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="bcba4-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="bcba4-105">Blazor는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bcba4-106">앱은 기본 제공 서비스를 구성 요소에 삽입 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="bcba4-107">앱은 사용자 지정 서비스를 정의 및 등록 하 고 DI를 통해 앱 전체에서 사용할 수 있게 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="bcba4-108">DI는 중앙 위치에 구성 된 서비스에 액세스 하기 위한 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="bcba4-109">Blazor 앱에서 다음과 같은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="bcba4-110">단일 서비스 라고 하는 여러 구성 요소에서 서비스 클래스의 단일 인스턴스를 공유 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="bcba4-111">참조 추상화를 사용 하 여 구체적인 서비스 클래스의 구성 요소를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="bcba4-112">예를 들어 앱의 데이터 `IDataAccess` 에 액세스 하기 위한 인터페이스를 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="bcba4-113">인터페이스는 구체적 `DataAccess` 클래스에서 구현 되 고 앱의 서비스 컨테이너에 서비스로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="bcba4-114">구성 요소가 DI를 사용 하 여 `IDataAccess` 구현을 수신 하는 경우 구성 요소가 구체적인 형식에 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="bcba4-115">단위 테스트에서 모의 구현을 위해 구현을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="bcba4-116">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="bcba4-116">Default services</span></span>

<span data-ttu-id="bcba4-117">기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="bcba4-118">서비스</span><span class="sxs-lookup"><span data-stu-id="bcba4-118">Service</span></span> | <span data-ttu-id="bcba4-119">수명</span><span class="sxs-lookup"><span data-stu-id="bcba4-119">Lifetime</span></span> | <span data-ttu-id="bcba4-120">설명</span><span class="sxs-lookup"><span data-stu-id="bcba4-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="bcba4-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="bcba4-121">Singleton</span></span> | <span data-ttu-id="bcba4-122">URI로 식별 되는 리소스에서 http 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="bcba4-123">이 인스턴스 `HttpClient` 는 백그라운드에서 HTTP 트래픽을 처리 하는 데 브라우저를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="bcba4-124">[BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) 는 앱의 기본 URI 접두사로 자동으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="bcba4-125">자세한 내용은 <xref:blazor/call-web-api>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bcba4-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="bcba4-126">Singleton</span><span class="sxs-lookup"><span data-stu-id="bcba4-126">Singleton</span></span> | <span data-ttu-id="bcba4-127">JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="bcba4-128">자세한 내용은 <xref:blazor/javascript-interop>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bcba4-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="bcba4-129">Singleton</span><span class="sxs-lookup"><span data-stu-id="bcba4-129">Singleton</span></span> | <span data-ttu-id="bcba4-130">Uri 및 탐색 상태를 사용 하기 위한 도우미를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="bcba4-131">자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/routing#uri-and-navigation-state-helpers)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="bcba4-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="bcba4-132">사용자 지정 서비스 공급자는 테이블에 나열 된 기본 서비스를 자동으로 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="bcba4-133">사용자 지정 서비스 공급자를 사용 하 고 표에 표시 된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="bcba4-134">앱에 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="bcba4-134">Add services to an app</span></span>

<span data-ttu-id="bcba4-135">새 앱을 만든 후 메서드를 검사 `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="bcba4-136">메서드에는 서비스 설명자 개체 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>의 목록 (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>)이 전달 됩니다. `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="bcba4-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="bcba4-137">서비스 설명자를 서비스 컬렉션에 제공 하 여 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="bcba4-138">다음 예제에서는 `IDataAccess` 인터페이스 및 구체적인 구현 `DataAccess`에 대 한 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="bcba4-139">다음 표에 표시 된 수명을 사용 하 여 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="bcba4-140">수명</span><span class="sxs-lookup"><span data-stu-id="bcba4-140">Lifetime</span></span> | <span data-ttu-id="bcba4-141">Description</span><span class="sxs-lookup"><span data-stu-id="bcba4-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="bcba4-142">Blazor 클라이언트 쪽에는 현재 DI 범위 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-142">Blazor client-side doesn't currently have a concept of DI scopes.</span></span> <span data-ttu-id="bcba4-143">`Scoped`-등록 된 서비스는 `Singleton` 서비스 처럼 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="bcba4-144">그러나 서버 쪽 호스팅 모델은 `Scoped` 수명을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-144">However, the server-side hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="bcba4-145">Razor 구성 요소에서 범위가 지정 된 서비스 등록의 범위는 연결로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-145">In a Razor component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="bcba4-146">따라서 현재 사용자로 범위를 지정 해야 하는 서비스에 대해 범위 지정 서비스를 사용 하는 것이 좋습니다. 현재 의도는 브라우저에서 클라이언트 쪽을 실행 하는 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="bcba4-147">DI는 서비스의 *단일 인스턴스* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="bcba4-148">서비스를 필요로 하 `Singleton` 는 모든 구성 요소는 동일한 서비스의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="bcba4-149">구성 요소가 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="bcba4-150">DI 시스템은 ASP.NET Core에서 DI 시스템을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="bcba4-151">자세한 내용은 <xref:fundamentals/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bcba4-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="bcba4-152">구성 요소에서 서비스 요청</span><span class="sxs-lookup"><span data-stu-id="bcba4-152">Request a service in a component</span></span>

<span data-ttu-id="bcba4-153">서비스 컬렉션에 서비스를 추가한 후에는 [ \@주입](xref:mvc/views/razor#inject) Razor 지시어를 사용 하 여 서비스를 구성 요소에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="bcba4-154">`@inject`에는 두 개의 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="bcba4-155">삽입할 &ndash; 서비스의 형식을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="bcba4-156">속성 &ndash; 삽입 된 app service를 받는 속성의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="bcba4-157">속성에는 수동으로 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="bcba4-158">컴파일러가 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-158">The compiler creates the property.</span></span>

<span data-ttu-id="bcba4-159">자세한 내용은 <xref:mvc/views/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bcba4-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="bcba4-160">여러 개의 `@inject` 문을 사용 하 여 여러 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="bcba4-161">다음 예제에서는 `@inject`을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="bcba4-162">을 구현 `Services.IDataAccess` 하는 서비스는 구성 요소의 속성 `DataRepository`에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="bcba4-163">코드에서 추상화를 `IDataAccess` 사용 하는 방법에 유의 하세요.</span><span class="sxs-lookup"><span data-stu-id="bcba4-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="bcba4-164">내부적으로 생성 된 속성 (`DataRepository`)은 `InjectAttribute` 특성을 사용 하 여 데코 레이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="bcba4-165">일반적으로이 특성은 직접 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="bcba4-166">구성 요소에 기본 클래스가 필요 하 고 기본 클래스에도 삽입 된 속성이 필요 하면를 수동으로 추가 합니다 `InjectAttribute`.</span><span class="sxs-lookup"><span data-stu-id="bcba4-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="bcba4-167">기본 클래스에서 파생 된 구성 요소에서는 `@inject` 지시문이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="bcba4-168">기본 `InjectAttribute` 클래스의는 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="bcba4-169">서비스에서 DI 사용</span><span class="sxs-lookup"><span data-stu-id="bcba4-169">Use DI in services</span></span>

<span data-ttu-id="bcba4-170">복잡 한 서비스에는 추가 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-170">Complex services might require additional services.</span></span> <span data-ttu-id="bcba4-171">이전 예제 `DataAccess` 에서에는 `HttpClient` 기본 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="bcba4-172">`@inject`(또는 `InjectAttribute`)를 서비스에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="bcba4-173">대신 *생성자 삽입* 을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="bcba4-174">서비스의 생성자에 매개 변수를 추가 하 여 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="bcba4-175">DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식 하 여 적절 하 게 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="bcba4-176">생성자 삽입에 대 한 필수 조건:</span><span class="sxs-lookup"><span data-stu-id="bcba4-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="bcba4-177">모든 인수를 DI에서 수행할 수 있는 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="bcba4-178">DI에서 다루지 않는 추가 매개 변수는 기본값을 지정 하는 경우 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="bcba4-179">적용 가능한 생성자는 *public*이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="bcba4-180">적용 가능한 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-180">One applicable constructor must exist.</span></span> <span data-ttu-id="bcba4-181">모호성을 발생 시 DI는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="bcba4-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bcba4-182">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bcba4-182">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
