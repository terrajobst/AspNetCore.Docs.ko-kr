---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor 앱이 서비스를 구성 요소에 삽입할 수 있는 방법을 참조 하세요.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 859fd484fc00104575f176fa7d3bf752895475a0
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885492"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="7f832-103">ASP.NET Core Blazor 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="7f832-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="7f832-104">[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="7f832-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7f832-105">Blazor는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7f832-106">앱은 기본 제공 서비스를 구성 요소에 삽입 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="7f832-107">앱은 사용자 지정 서비스를 정의 및 등록 하 고 DI를 통해 앱 전체에서 사용할 수 있게 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="7f832-108">DI는 중앙 위치에 구성 된 서비스에 액세스 하기 위한 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="7f832-109">Blazor 앱에서 다음과 같은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="7f832-110">단일 *서비스 라고 하는 여러* 구성 요소에서 서비스 클래스의 단일 인스턴스를 공유 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="7f832-111">참조 추상화를 사용 하 여 구체적인 서비스 클래스의 구성 요소를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="7f832-112">예를 들어 응용 프로그램의 데이터에 액세스 하는 `IDataAccess` 인터페이스를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="7f832-113">인터페이스는 구체적 `DataAccess` 클래스에 의해 구현 되 고 앱의 서비스 컨테이너에 서비스로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="7f832-114">구성 요소가 DI를 사용 하 여 `IDataAccess` 구현을 수신 하는 경우 구성 요소가 구체적인 형식에 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="7f832-115">단위 테스트에서 모의 구현을 위해 구현을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="7f832-116">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="7f832-116">Default services</span></span>

<span data-ttu-id="7f832-117">기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="7f832-118">서비스</span><span class="sxs-lookup"><span data-stu-id="7f832-118">Service</span></span> | <span data-ttu-id="7f832-119">수명</span><span class="sxs-lookup"><span data-stu-id="7f832-119">Lifetime</span></span> | <span data-ttu-id="7f832-120">설명</span><span class="sxs-lookup"><span data-stu-id="7f832-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="7f832-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="7f832-121">Singleton</span></span> | <span data-ttu-id="7f832-122">URI로 식별 되는 리소스에서 http 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="7f832-123">Blazor Weasembomapp의 `HttpClient` 인스턴스는 브라우저를 사용 하 여 백그라운드에서 HTTP 트래픽을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="7f832-124">Blazor 서버 앱에는 기본적으로 서비스로 구성 된 `HttpClient` 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="7f832-125">Blazor 서버 앱에 대 한 `HttpClient`를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="7f832-126">자세한 내용은 <xref:blazor/call-web-api>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f832-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="7f832-127">Singleton (Blazor weasembmbambome</span><span class="sxs-lookup"><span data-stu-id="7f832-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="7f832-128">범위가 지정 된 (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="7f832-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="7f832-129">JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="7f832-130">자세한 내용은 <xref:blazor/javascript-interop>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f832-130">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="7f832-131">Singleton (Blazor weasembmbambome</span><span class="sxs-lookup"><span data-stu-id="7f832-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="7f832-132">범위가 지정 된 (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="7f832-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="7f832-133">Uri 및 탐색 상태를 사용 하기 위한 도우미를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="7f832-134">자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/routing#uri-and-navigation-state-helpers)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7f832-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="7f832-135">사용자 지정 서비스 공급자는 테이블에 나열 된 기본 서비스를 자동으로 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="7f832-136">사용자 지정 서비스 공급자를 사용 하 고 표에 표시 된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="7f832-137">앱에 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="7f832-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="7f832-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7f832-138">Blazor WebAssembly</span></span>

<span data-ttu-id="7f832-139">*Program.cs*의 `Main` 메서드에서 앱의 서비스 컬렉션에 대 한 서비스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="7f832-140">다음 예제에서는 `MyDependency` 구현이 `IMyDependency`에 대해 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="7f832-141">호스트를 빌드한 후에는 구성 요소를 렌더링 하기 전에 루트 DI 범위에서 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="7f832-142">이는 콘텐츠를 렌더링 하기 전에 초기화 논리를 실행 하는 데 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="7f832-143">또한 호스트는 앱에 대 한 중앙 구성 인스턴스도 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="7f832-144">이전 예제를 기반으로 하는 날씨 서비스의 URL은 기본 구성 원본 (예: *appsettings*)에서 `InitializeWeatherAsync`으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="7f832-145">Blazor 서버</span><span class="sxs-lookup"><span data-stu-id="7f832-145">Blazor Server</span></span>

<span data-ttu-id="7f832-146">새 앱을 만든 후 `Startup.ConfigureServices` 메서드를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="7f832-147">`ConfigureServices` 메서드에는<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>(서비스 설명자 개체) 목록 인 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="7f832-148">서비스 설명자를 서비스 컬렉션에 제공 하 여 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="7f832-149">다음 예제에서는 `IDataAccess` 인터페이스와 해당 `DataAccess`의 구체적인 구현에 대 한 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="7f832-150">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="7f832-150">Service lifetime</span></span>

<span data-ttu-id="7f832-151">다음 표에 표시 된 수명을 사용 하 여 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="7f832-152">수명</span><span class="sxs-lookup"><span data-stu-id="7f832-152">Lifetime</span></span> | <span data-ttu-id="7f832-153">설명</span><span class="sxs-lookup"><span data-stu-id="7f832-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="7f832-154"> Weasembomapps는 현재 DI 범위의 개념을가지고 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="7f832-155">`Scoped`등록 된 서비스는 `Singleton` 서비스 처럼 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="7f832-156">그러나 Blazor 서버 호스팅 모델은 `Scoped` 수명을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="7f832-157">Blazor Server 앱에서 범위가 지정 된 서비스 등록 범위는 *연결*로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="7f832-158">따라서 현재 사용자로 범위를 지정 해야 하는 서비스에 대해 범위 지정 서비스를 사용 하는 것이 좋습니다. 현재 의도는 브라우저에서 클라이언트 쪽을 실행 하는 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="7f832-159">DI는 서비스의 *단일 인스턴스* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="7f832-160">`Singleton` 서비스를 필요로 하는 모든 구성 요소는 동일한 서비스의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="7f832-161">구성 요소가 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="7f832-162">DI 시스템은 ASP.NET Core에서 DI 시스템을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="7f832-163">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f832-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="7f832-164">구성 요소에서 서비스 요청</span><span class="sxs-lookup"><span data-stu-id="7f832-164">Request a service in a component</span></span>

<span data-ttu-id="7f832-165">서비스 컬렉션에 서비스를 추가한 후에는 [\@주입](xref:mvc/views/razor#inject) Razor 지시어를 사용 하 여 서비스를 구성 요소에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="7f832-166">`@inject`에는 다음과 같은 두 개의 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="7f832-167">삽입할 서비스의 형식 &ndash;를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="7f832-168">속성 &ndash; 삽입 된 app service를 받는 속성의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="7f832-169">속성에는 수동으로 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="7f832-170">컴파일러가 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-170">The compiler creates the property.</span></span>

<span data-ttu-id="7f832-171">자세한 내용은 <xref:mvc/views/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f832-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="7f832-172">여러 `@inject` 문을 사용 하 여 여러 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="7f832-173">다음 예제에서는 `@inject`을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="7f832-174">`Services.IDataAccess` 구현 하는 서비스는 구성 요소의 속성 `DataRepository`에 삽입 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="7f832-175">코드가 `IDataAccess` 추상화를 사용 하는 방법에 유의 하세요.</span><span class="sxs-lookup"><span data-stu-id="7f832-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="7f832-176">내부적으로 생성 된 속성 (`DataRepository`)은 `InjectAttribute` 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="7f832-177">일반적으로이 특성은 직접 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="7f832-178">구성 요소에 기본 클래스가 필요 하 고 기본 클래스에도 삽입 된 속성이 필요 하면 `InjectAttribute`를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="7f832-179">기본 클래스에서 파생 된 구성 요소에서는 `@inject` 지시문이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="7f832-180">기본 클래스의 `InjectAttribute` 면 충분 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="7f832-181">서비스에서 DI 사용</span><span class="sxs-lookup"><span data-stu-id="7f832-181">Use DI in services</span></span>

<span data-ttu-id="7f832-182">복잡 한 서비스에는 추가 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-182">Complex services might require additional services.</span></span> <span data-ttu-id="7f832-183">이전 예제에서는 `DataAccess` `HttpClient` 기본 서비스를 요구할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="7f832-184">`@inject` (또는 `InjectAttribute`)를 서비스에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="7f832-185">대신 *생성자 삽입* 을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="7f832-186">서비스의 생성자에 매개 변수를 추가 하 여 필요한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="7f832-187">DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식 하 여 적절 하 게 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="7f832-188">생성자 삽입에 대 한 필수 조건:</span><span class="sxs-lookup"><span data-stu-id="7f832-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="7f832-189">모든 인수를 DI에서 수행할 수 있는 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="7f832-190">DI에서 다루지 않는 추가 매개 변수는 기본값을 지정 하는 경우 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="7f832-191">적용 가능한 생성자는 *public*이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="7f832-192">적용 가능한 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-192">One applicable constructor must exist.</span></span> <span data-ttu-id="7f832-193">모호성을 발생 시 DI는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="7f832-194">DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="7f832-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="7f832-195">ASP.NET Core 앱에서 범위가 지정 된 서비스는 일반적으로 현재 요청으로 범위가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="7f832-196">요청이 완료 된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에 의해 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="7f832-197">Blazor Server 앱에서 요청 범위는 클라이언트 연결 기간 동안 지속 되므로 임시 및 범위가 지정 된 서비스가 예상 보다 훨씬 오래 지속 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="7f832-198">구성 요소의 수명으로 서비스 범위를 표시 하려면 `OwningComponentBase` 및 `OwningComponentBase<TService>` 기본 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-198">To scope services to the lifetime of a component, you can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="7f832-199">이러한 기본 클래스는 구성 요소의 수명으로 범위가 지정 된 서비스를 확인 하는 `IServiceProvider` 형식의 `ScopedServices` 속성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-199">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="7f832-200">Razor의 기본 클래스에서 상속 되는 구성 요소를 작성 하려면 `@inherits` 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-200">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

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
> <span data-ttu-id="7f832-201">`@inject` 또는 `InjectAttribute`를 사용 하 여 구성 요소에 삽입 되는 서비스는 구성 요소의 범위에 생성 되지 않으며 요청 범위에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f832-201">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f832-202">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7f832-202">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
