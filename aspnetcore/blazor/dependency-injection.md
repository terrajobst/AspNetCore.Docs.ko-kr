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
ms.openlocfilehash: fa6762522c831c7fbe2742dbfe4e25a377988e1e
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869566"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="d3c6f-103">ASP.NET Core Blazor 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="d3c6f-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="d3c6f-104">[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="d3c6f-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d3c6f-105">Blazor는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d3c6f-106">앱은 기본 제공 서비스를 구성 요소에 삽입 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="d3c6f-107">앱은 사용자 지정 서비스를 정의 및 등록 하 고 DI를 통해 앱 전체에서 사용할 수 있게 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="d3c6f-108">DI는 중앙 위치에 구성 된 서비스에 액세스 하기 위한 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="d3c6f-109">Blazor 앱에서 다음과 같은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="d3c6f-110">단일 *서비스 라고 하는 여러* 구성 요소에서 서비스 클래스의 단일 인스턴스를 공유 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="d3c6f-111">참조 추상화를 사용 하 여 구체적인 서비스 클래스의 구성 요소를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="d3c6f-112">예를 들어 응용 프로그램의 데이터에 액세스 하는 `IDataAccess` 인터페이스를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="d3c6f-113">인터페이스는 구체적 `DataAccess` 클래스에 의해 구현 되 고 앱의 서비스 컨테이너에 서비스로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="d3c6f-114">구성 요소가 DI를 사용 하 여 `IDataAccess` 구현을 수신 하는 경우 구성 요소가 구체적인 형식에 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="d3c6f-115">단위 테스트에서 모의 구현을 위해 구현을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="d3c6f-116">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="d3c6f-116">Default services</span></span>

<span data-ttu-id="d3c6f-117">기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="d3c6f-118">서비스</span><span class="sxs-lookup"><span data-stu-id="d3c6f-118">Service</span></span> | <span data-ttu-id="d3c6f-119">수명</span><span class="sxs-lookup"><span data-stu-id="d3c6f-119">Lifetime</span></span> | <span data-ttu-id="d3c6f-120">설명</span><span class="sxs-lookup"><span data-stu-id="d3c6f-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="d3c6f-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="d3c6f-121">Singleton</span></span> | <span data-ttu-id="d3c6f-122">URI로 식별 되는 리소스에서 http 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="d3c6f-123">Blazor Weasembomapp의 `HttpClient` 인스턴스는 브라우저를 사용 하 여 백그라운드에서 HTTP 트래픽을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="d3c6f-124">Blazor 서버 앱에는 기본적으로 서비스로 구성 된 `HttpClient` 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="d3c6f-125">Blazor 서버 앱에 대 한 `HttpClient`를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="d3c6f-126">자세한 내용은 <xref:blazor/call-web-api>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="d3c6f-127">Singleton (Blazor weasembmbambome</span><span class="sxs-lookup"><span data-stu-id="d3c6f-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="d3c6f-128">범위가 지정 된 (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="d3c6f-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="d3c6f-129">JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="d3c6f-130">자세한 내용은 <xref:blazor/javascript-interop>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-130">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="d3c6f-131">Singleton (Blazor weasembmbambome</span><span class="sxs-lookup"><span data-stu-id="d3c6f-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="d3c6f-132">범위가 지정 된 (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="d3c6f-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="d3c6f-133">Uri 및 탐색 상태를 사용 하기 위한 도우미를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="d3c6f-134">자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/routing#uri-and-navigation-state-helpers)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="d3c6f-135">사용자 지정 서비스 공급자는 테이블에 나열 된 기본 서비스를 자동으로 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="d3c6f-136">사용자 지정 서비스 공급자를 사용 하 고 표에 표시 된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="d3c6f-137">앱에 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="d3c6f-137">Add services to an app</span></span>

<span data-ttu-id="d3c6f-138">새 앱을 만든 후 `Startup.ConfigureServices` 메서드를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-138">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="d3c6f-139">`ConfigureServices` 메서드에는<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>(서비스 설명자 개체) 목록 인 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-139">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="d3c6f-140">서비스 설명자를 서비스 컬렉션에 제공 하 여 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-140">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="d3c6f-141">다음 예제에서는 `IDataAccess` 인터페이스와 해당 `DataAccess`의 구체적인 구현에 대 한 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="d3c6f-142">다음 표에 표시 된 수명을 사용 하 여 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-142">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="d3c6f-143">수명</span><span class="sxs-lookup"><span data-stu-id="d3c6f-143">Lifetime</span></span> | <span data-ttu-id="d3c6f-144">설명</span><span class="sxs-lookup"><span data-stu-id="d3c6f-144">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="d3c6f-145"> Weasembomapps는 현재 DI 범위의 개념을가지고 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-145"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="d3c6f-146">`Scoped`등록 된 서비스는 `Singleton` 서비스 처럼 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-146">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="d3c6f-147">그러나 Blazor 서버 호스팅 모델은 `Scoped` 수명을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-147">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="d3c6f-148">Blazor Server 앱에서 범위가 지정 된 서비스 등록 범위는 *연결*로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-148">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="d3c6f-149">따라서 현재 사용자로 범위를 지정 해야 하는 서비스에 대해 범위 지정 서비스를 사용 하는 것이 좋습니다. 현재 의도는 브라우저에서 클라이언트 쪽을 실행 하는 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-149">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="d3c6f-150">DI는 서비스의 *단일 인스턴스* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-150">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="d3c6f-151">`Singleton` 서비스를 필요로 하는 모든 구성 요소는 동일한 서비스의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-151">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="d3c6f-152">구성 요소가 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-152">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="d3c6f-153">DI 시스템은 ASP.NET Core에서 DI 시스템을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-153">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="d3c6f-154">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-154">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="d3c6f-155">구성 요소에서 서비스 요청</span><span class="sxs-lookup"><span data-stu-id="d3c6f-155">Request a service in a component</span></span>

<span data-ttu-id="d3c6f-156">서비스 컬렉션에 서비스를 추가한 후에는 [\@주입](xref:mvc/views/razor#inject) Razor 지시어를 사용 하 여 서비스를 구성 요소에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-156">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="d3c6f-157">`@inject`에는 다음과 같은 두 개의 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-157">`@inject` has two parameters:</span></span>

* <span data-ttu-id="d3c6f-158">삽입할 서비스의 형식 &ndash;를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-158">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="d3c6f-159">속성 &ndash; 삽입 된 app service를 받는 속성의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-159">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="d3c6f-160">속성에는 수동으로 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-160">The property doesn't require manual creation.</span></span> <span data-ttu-id="d3c6f-161">컴파일러가 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-161">The compiler creates the property.</span></span>

<span data-ttu-id="d3c6f-162">자세한 내용은 <xref:mvc/views/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-162">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="d3c6f-163">여러 `@inject` 문을 사용 하 여 여러 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-163">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="d3c6f-164">다음 예제에서는 `@inject`을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-164">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="d3c6f-165">`Services.IDataAccess` 구현 하는 서비스는 구성 요소의 속성 `DataRepository`에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-165">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="d3c6f-166">코드가 `IDataAccess` 추상화를 사용 하는 방법에 유의 하세요.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-166">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="d3c6f-167">내부적으로 생성 된 속성 (`DataRepository`)은 `InjectAttribute` 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-167">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="d3c6f-168">일반적으로이 특성은 직접 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-168">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="d3c6f-169">구성 요소에 기본 클래스가 필요 하 고 기본 클래스에도 삽입 된 속성이 필요 하면 `InjectAttribute`를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-169">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="d3c6f-170">기본 클래스에서 파생 된 구성 요소에서는 `@inject` 지시문이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-170">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="d3c6f-171">기본 클래스의 `InjectAttribute` 면 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-171">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="d3c6f-172">서비스에서 DI 사용</span><span class="sxs-lookup"><span data-stu-id="d3c6f-172">Use DI in services</span></span>

<span data-ttu-id="d3c6f-173">복잡 한 서비스에는 추가 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-173">Complex services might require additional services.</span></span> <span data-ttu-id="d3c6f-174">이전 예제에서는 `DataAccess` `HttpClient` 기본 서비스를 요구할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-174">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="d3c6f-175">`@inject` (또는 `InjectAttribute`)를 서비스에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-175">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="d3c6f-176">대신 *생성자 삽입* 을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-176">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="d3c6f-177">서비스의 생성자에 매개 변수를 추가 하 여 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-177">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="d3c6f-178">DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식 하 여 적절 하 게 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-178">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="d3c6f-179">생성자 삽입에 대 한 필수 조건:</span><span class="sxs-lookup"><span data-stu-id="d3c6f-179">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="d3c6f-180">모든 인수를 DI에서 수행할 수 있는 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-180">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="d3c6f-181">DI에서 다루지 않는 추가 매개 변수는 기본값을 지정 하는 경우 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-181">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="d3c6f-182">적용 가능한 생성자는 *public*이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-182">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="d3c6f-183">적용 가능한 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-183">One applicable constructor must exist.</span></span> <span data-ttu-id="d3c6f-184">모호성을 발생 시 DI는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-184">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="d3c6f-185">DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="d3c6f-185">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="d3c6f-186">ASP.NET Core 앱에서 범위가 지정 된 서비스는 일반적으로 현재 요청으로 범위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-186">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="d3c6f-187">요청이 완료 된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에 의해 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-187">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="d3c6f-188">Blazor Server 앱에서 요청 범위는 클라이언트 연결 기간 동안 지속 되므로 임시 및 범위가 지정 된 서비스가 예상 보다 훨씬 오래 지속 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-188">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="d3c6f-189">구성 요소의 수명으로 서비스 범위를 표시 하려면 `OwningComponentBase` 및 `OwningComponentBase<TService>` 기본 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-189">To scope services to the lifetime of a component, you can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="d3c6f-190">이러한 기본 클래스는 구성 요소의 수명으로 범위가 지정 된 서비스를 확인 하는 `IServiceProvider` 형식의 `ScopedServices` 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-190">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="d3c6f-191">Razor의 기본 클래스에서 상속 되는 구성 요소를 작성 하려면 `@inherits` 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-191">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

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
> <span data-ttu-id="d3c6f-192">`@inject` 또는 `InjectAttribute`를 사용 하 여 구성 요소에 삽입 되는 서비스는 구성 요소의 범위에 생성 되지 않으며 요청 범위에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3c6f-192">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3c6f-193">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d3c6f-193">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
