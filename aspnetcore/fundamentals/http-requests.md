---
title: ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기
author: stevejgordon
description: IHttpClientFactory 인터페이스를 사용하여 ASP.NET Core에서 논리적 HttpClient 인스턴스를 관리하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 8b95f63c0e06a2b7d1d66064def192f91b8ffbb4
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874954"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="c7ff8-103">ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="c7ff8-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

<span data-ttu-id="c7ff8-104">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="c7ff8-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="c7ff8-105">앱에서 <xref:System.Net.Http.HttpClient> 인스턴스를 만들고 구성하려면 <xref:System.Net.Http.IHttpClientFactory>를 등록하고 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c7ff8-106">다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-106">It offers the following benefits:</span></span>

* <span data-ttu-id="c7ff8-107">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위해 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c7ff8-108">예를 들어, *github* 클라이언트는 [GitHub](https://github.com/)에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-108">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c7ff8-109">다른 용도로 기본 클라이언트를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-109">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c7ff8-110">`HttpClient`에서 처리기 위임을 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="c7ff8-111">풀링 및 기본의 수명을 관리 수동으로 `HttpClient` 수명을 관리하는 경우 발생하는 일반적인 DNS 문제를 방지하려면 기본 `HttpClientMessageHandler` 인스턴스의 수명 및 풀링을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-111">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c7ff8-112">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 환경(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-112">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c7ff8-113">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7ff8-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range="<= aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="c7ff8-114">전제 조건</span><span class="sxs-lookup"><span data-stu-id="c7ff8-114">Prerequisites</span></span>

<span data-ttu-id="c7ff8-115">.NET Framework를 대상으로 하는 프로젝트에는 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-115">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="c7ff8-116">.NET Core를 대상으로 하며 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하는 프로젝트에는 `Microsoft.Extensions.Http` 패키지가 이미 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-116">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

::: moniker-end

## <a name="consumption-patterns"></a><span data-ttu-id="c7ff8-117">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="c7ff8-117">Consumption patterns</span></span>

<span data-ttu-id="c7ff8-118">앱에서 사용할 수 있는 여러 방법 `IHttpClientFactory`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-118">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c7ff8-119">기본 사용</span><span class="sxs-lookup"><span data-stu-id="c7ff8-119">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c7ff8-120">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c7ff8-120">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c7ff8-121">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c7ff8-121">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c7ff8-122">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c7ff8-122">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c7ff8-123">어느 것도 다른 것에 비해 월등히 우수하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-123">None of them are strictly superior to another.</span></span> <span data-ttu-id="c7ff8-124">가장 좋은 방법은 앱의 제약 조건에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-124">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c7ff8-125">기본 사용</span><span class="sxs-lookup"><span data-stu-id="c7ff8-125">Basic usage</span></span>

<span data-ttu-id="c7ff8-126">`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`에 있는 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-126">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c7ff8-127">일단 등록하면 코드는 서비스가 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 주입할 수 있는 어느 곳에서나 `IHttpClientFactory`를 수락할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-127">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c7ff8-128">`IHttpClientFactory`은 `HttpClient` 인스턴스를 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-128">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c7ff8-129">이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-129">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="c7ff8-130">`HttpClient`이 사용되는 방식에 아무 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-130">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="c7ff8-131">현재 `HttpClient` 인스턴스를 만드는 위치에서 이러한 발생을 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-131">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c7ff8-132">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c7ff8-132">Named clients</span></span>

<span data-ttu-id="c7ff8-133">앱이 각각 구성이 다른 `HttpClient`의 다양한 사용을 요구하는 경우 옵션은 **명명된 클라이언트**를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-133">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="c7ff8-134">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-134">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c7ff8-135">위의 코드에서는 `AddHttpClient`를 호출하여 *github* 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-135">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="c7ff8-136">이 클라이언트에는 적용된 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-136">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="c7ff8-137">`CreateClient`을 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-137">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="c7ff8-138">명명된 클라이언트를 사용하려면 문자열 매개 변수는 `CreateClient`에 전달될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-138">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="c7ff8-139">만들 클라이언트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-139">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c7ff8-140">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-140">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c7ff8-141">클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-141">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c7ff8-142">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c7ff8-142">Typed clients</span></span>

<span data-ttu-id="c7ff8-143">형식화된 클라이언트는 문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-143">Typed clients provide the same capabilities as named clients without the need to use strings as keys.</span></span> <span data-ttu-id="c7ff8-144">형식화된 클라이언트 방법은 클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-144">The typed client approach provides IntelliSense and compiler help when consuming clients.</span></span> <span data-ttu-id="c7ff8-145">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-145">They provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c7ff8-146">예를 들어 단일 형식의 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 처리하는 모든 논리를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-146">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span> <span data-ttu-id="c7ff8-147">다른 이점은 DI로 작업하고 앱에서 필요할 경우 삽입할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-147">Another advantage is that they work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="c7ff8-148">형식화된 클라이언트는 생성자의 `HttpClient` 매개 변수를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-148">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c7ff8-149">위의 코드에서 구성은 형식화된 클라이언트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-149">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="c7ff8-150">`HttpClient` 개체는 공용 속성으로 공개됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-150">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="c7ff8-151">`HttpClient` 기능을 공개하는 API 관련 메서드를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-151">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c7ff8-152">`GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-152">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="c7ff8-153">형식화된 클라이언트를 등록하려면 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드는 형식화된 클라이언트 클래스를 지정하면서 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-153">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c7ff8-154">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-154">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c7ff8-155">형식화된 클라이언트는 직접 삽입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-155">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c7ff8-156">원할 경우 형식화된 클라이언트에 대한 구성은 형식화된 클라이언트의 생성자보다는 `Startup.ConfigureServices`에 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-156">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c7ff8-157">형식화된 클라이언트 내에서 `HttpClient`을 완전히 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-157">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="c7ff8-158">속성으로 공개하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-158">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c7ff8-159">위의 코드에서 `HttpClient`은 개인 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-159">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="c7ff8-160">외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-160">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c7ff8-161">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c7ff8-161">Generated clients</span></span>

<span data-ttu-id="c7ff8-162">`IHttpClientFactory`은 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-162">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c7ff8-163">Refit은 .NET에 대한 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-163">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c7ff8-164">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-164">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c7ff8-165">인터페이스의 구현은 외부 HTTP를 호출하려면 `HttpClient`를 사용하여 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-165">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c7ff8-166">인터페이스와 회신은 외부 API와 해당 응답을 나타내기 위해 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-166">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="c7ff8-167">형식화된 클라이언트는 구현을 생성하기 위해 Refit를 사용하여 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-167">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="c7ff8-168">DI 및 Refit에서 제공한 구현을 통해 필요한 경우 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-168">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c7ff8-169">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="c7ff8-169">Outgoing request middleware</span></span>

<span data-ttu-id="c7ff8-170">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 처리기 위임이라는 개념이 이미 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-170">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c7ff8-171">`IHttpClientFactory`은 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-171">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="c7ff8-172">나가는 요청 미들웨어 파이프라인을 빌드하려면 여러 처리기의 연결 및 등록을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-172">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c7ff8-173">이러한 처리기 각각은 나가는 요청 전후 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-173">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c7ff8-174">이 패턴은 ASP.NET Core에서 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-174">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c7ff8-175">패턴은 캐싱, 오류 처리, serialization 및 로깅을 포함하여 HTTP 요청을 둘러싼 교차 편집 문제를 관리할 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-175">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="c7ff8-176">처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-176">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="c7ff8-177">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-177">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c7ff8-178">위의 코드에서는 기본 처리기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-178">The preceding code defines a basic handler.</span></span> <span data-ttu-id="c7ff8-179">`X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-179">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="c7ff8-180">헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-180">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="c7ff8-181">등록 동안 하나 이상의 처리기가 `HttpClient`에 대한 구성에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-181">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="c7ff8-182">이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>에 대한 확장 메서드를 통해 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-182">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c7ff8-183">위의 코드에서 `ValidateHeaderHandler`은 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-183">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c7ff8-184">`IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-184">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="c7ff8-185">처리기는 모든 범위의 서비스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-185">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="c7ff8-186">처리기가 삭제되면 처리기에서 사용하는 서비스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-186">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="c7ff8-187">일단 등록되면 처리기에 대한 형식으로 전달하면서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>을 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-187">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c7ff8-188">위의 코드에서 `ValidateHeaderHandler`은 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-188">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c7ff8-189">처리기는 범위가 지정되지 않은, 임시 서비스로 DI에 등록**되어야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-189">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="c7ff8-190">처리기가 범위가 지정된 서비스로 등록되고 처리기에서 사용하는 서비스가 삭제 가능한 경우, 처리기가 범위를 벗어나기 전에 처리기의 서비스가 삭제될 수 있으며 그러면 처리기에서 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-190">If the handler is registered as a scoped service and any services that the handler depends upon are disposable, the handler's services could be disposed before the handler goes out of scope, which would cause the handler to fail.</span></span>

<span data-ttu-id="c7ff8-191">일단 등록되면 처리기 형식으로 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-191">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

::: moniker-end

<span data-ttu-id="c7ff8-192">여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-192">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c7ff8-193">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-193">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c7ff8-194">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-194">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c7ff8-195">`HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-195">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="c7ff8-196">`IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-196">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="c7ff8-197">사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-197">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c7ff8-198">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="c7ff8-198">Use Polly-based handlers</span></span>

<span data-ttu-id="c7ff8-199">`IHttpClientFactory`은 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-199">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c7ff8-200">Polly는 .NET에 대한 포괄적인 복원 력 및 일시적인 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-200">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c7ff8-201">Polly는 개발자가 다시 시도, 회로 차단기, 시간 제한, Bulkhead 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-201">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c7ff8-202">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-202">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c7ff8-203">Polly 확장:</span><span class="sxs-lookup"><span data-stu-id="c7ff8-203">The Polly extensions:</span></span>

* <span data-ttu-id="c7ff8-204">클라이언트에 Polly 기반 처리기 추가를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-204">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="c7ff8-205">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-205">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="c7ff8-206">이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-206">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c7ff8-207">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="c7ff8-207">Handle transient faults</span></span>

<span data-ttu-id="c7ff8-208">가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-208">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c7ff8-209">일시적인 오류를 처리하도록 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`이라고 하는 편리한 확장 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-209">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c7ff8-210">이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-210">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="c7ff8-211">`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-211">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c7ff8-212">확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-212">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="c7ff8-213">위의 코드에서 `WaitAndRetryAsync` 정책이 정의되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-213">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c7ff8-214">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-214">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c7ff8-215">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="c7ff8-215">Dynamically select policies</span></span>

<span data-ttu-id="c7ff8-216">Polly 기반 처리기를 추가하는 데 사용될 수 있는 추가 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-216">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="c7ff8-217">이러한 하나의 확장은 여러 오버로드가 있는 `AddPolicyHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-217">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="c7ff8-218">하나의 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-218">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c7ff8-219">앞의 코드에서 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-219">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c7ff8-220">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-220">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c7ff8-221">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="c7ff8-221">Add multiple Polly handlers</span></span>

<span data-ttu-id="c7ff8-222">향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-222">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c7ff8-223">앞의 예제에서 두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-223">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="c7ff8-224">첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-224">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="c7ff8-225">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-225">Failed requests are retried up to three times.</span></span> <span data-ttu-id="c7ff8-226">`AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-226">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="c7ff8-227">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-227">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="c7ff8-228">회로 차단기 정책은 상태 저장입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-228">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c7ff8-229">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-229">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c7ff8-230">Polly 레지스트리에서 정책 추가</span><span class="sxs-lookup"><span data-stu-id="c7ff8-230">Add policies from the Polly registry</span></span>

<span data-ttu-id="c7ff8-231">정기적으로 사용되는 정책의 관리 방식은 정책을 한 번 정의하고 `PolicyRegistry`에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-231">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="c7ff8-232">정책을 사용하여 레지스트리에서 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-232">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="c7ff8-233">위의 코드에서 두 정책은 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-233">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="c7ff8-234">레지스트리에서 정책을 사용하기 위해 `AddPolicyHandlerFromRegistry` 메서드가 사용되어 적용할 정책의 이름을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-234">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="c7ff8-235">`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-235">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c7ff8-236">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="c7ff8-236">HttpClient and lifetime management</span></span>

<span data-ttu-id="c7ff8-237">`CreateClient`가 `IHttpClientFactory`에서 호출될 때마다 새 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-237">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c7ff8-238">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-238">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="c7ff8-239">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-239">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c7ff8-240">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-240">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c7ff8-241">새 `HttpClient` 인스턴스의 수명이 만료되지 않은 경우 해당 인스턴스를 만들 때 `HttpMessageHandler` 인스턴스를 풀에서 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-241">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c7ff8-242">일반적으로 각 처리기가 자체 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-242">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c7ff8-243">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-243">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c7ff8-244">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-244">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="c7ff8-245">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-245">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c7ff8-246">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-246">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="c7ff8-247">재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-247">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="c7ff8-248">클라이언트의 삭제는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-248">Disposal of the client isn't required.</span></span> <span data-ttu-id="c7ff8-249">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-249">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c7ff8-250">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-250">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="c7ff8-251">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 처리될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-251">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="c7ff8-252">긴 기간 동안 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`의 시작 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-252">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c7ff8-253">이 패턴은 `IHttpClientFactory`로 마이그레이션한 후에는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-253">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="c7ff8-254">로깅</span><span class="sxs-lookup"><span data-stu-id="c7ff8-254">Logging</span></span>

<span data-ttu-id="c7ff8-255">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-255">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c7ff8-256">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-256">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="c7ff8-257">요청 헤더의 로깅 등의 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-257">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c7ff8-258">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-258">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c7ff8-259">예를 들어, *MyNamedClient*라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-259">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="c7ff8-260">*LogicalHandler*라는 접미사가 있는 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-260">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c7ff8-261">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-261">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c7ff8-262">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-262">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c7ff8-263">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-263">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c7ff8-264">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-264">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="c7ff8-265">요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-265">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="c7ff8-266">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-266">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c7ff8-267">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-267">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c7ff8-268">예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-268">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="c7ff8-269">로그 범주에 클라이언트의 이름을 포함하는 것은 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-269">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c7ff8-270">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="c7ff8-270">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c7ff8-271">클라이언트에서 사용한 내부 `HttpMessageHandler`의 구성을 제어하는 것이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-271">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c7ff8-272">`IHttpClientBuilder`은 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-272">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c7ff8-273"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-273">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c7ff8-274">대리자는 해당 클라이언트가 사용한 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7ff8-274">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="additional-resources"></a><span data-ttu-id="c7ff8-275">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c7ff8-275">Additional resources</span></span>

* [<span data-ttu-id="c7ff8-276">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="c7ff8-276">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c7ff8-277">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="c7ff8-277">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c7ff8-278">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="c7ff8-278">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
